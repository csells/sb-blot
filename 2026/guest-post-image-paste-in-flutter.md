---
tags: flutter
---
# Guest Post: Enabling Image Paste in Flutter

How a simple feature request led to a deep dive into AppKit, responders, and the creation of the mac_menu_bar plugin.

By Emmanuel David Tuksa ([@DeTuksa](https://github.com/DeTuksa))

## Foreword

*Before we dive into the technical details, I want to extend a massive thank you to Chris Sells (@csells). This journey started with a single feature request on the `flutter_ai_toolkit` repo, and Chris’s guidance, curiosity, and “what if” questions were the catalyst for the solutions I am sharing today. I am incredibly grateful for his support throughout the development of the* *`mac_menu_bar`* *plugin and for the opportunity to share this story here on his blog.*

## Introduction

When I saw the feature request in the[ flutter_ai_toolkit](https://github.com/flutter/ai) repository, “**Add the ability to paste an image from the clipboard into the text box**”, I thought it would be a straightforward weekend project. Little did I know this task would lead me down a rabbit hole of platform-specific limitations, eventually resulting in the creation of a new Flutter plugin:[ mac_menu_bar](https://pub.dev/packages/mac_menu_bar).

> [!NOTE]
>
> As of the publication of this blog post, [the Feature/paste image and text from clipboard PR](https://github.com/flutter/ai/pull/173) has not yet been accepted by the Flutter team for inclusion in the Flutter AI Toolkit. However, Emmanuel has been kind enough to apply this same feature to [**the dartantic_chat package**](https://pub.dev/packages/dartantic_chat) (with the ability to drag 'n' drop images into chat coming soon), a fork of the chat widget simplified for use with dartantic agents and providers. Thanks to Emmanuel for his excellent work and this blog post!
>
> --Chris

To make pasting feel “native”, it has to work via three distinct paths:

- Keyboard shortcuts (Cmd+V)
- Context menus (right-click -> Paste)
- The system menu bar (Edit -> Paste)

If even one of these paths behaves differently, the user experience breaks. The Flutter Clipboard API is intentionally minimal; it works great for text, but images and richer data types quickly fall outside its scope. My first step was seeking a package that could handle binary data.

After discussing it with Chris, we initially looked at [pasteboard](https://pub.dev/packages/pasteboard). It was a good start, but it hit a wall quickly:

- Web Limitations: It couldn’t handle local images on the web.
- The “Hijack” Problem: It couldn’t intercept the system’s native “Paste” commands universally.

Since the [pasteboard](https://pub.dev/packages/pasteboard) package couldn’t handle images on the web properly, I fell back to the Flutter web package and using the dart:js_interop import to convert JS types to Dart, but the maintenance burden was high, and its fragility and inconsistency made it clear that it wasn’t a long-term fix.

Following a suggestion from the issue conversation, I switched to [super_clipboard](https://pub.dev/packages/super_clipboard). This was a game-changer as it provided the cross-platform support I needed for binary data, and I felt I was 90% of the way there. But then I noticed a glaring issue on macOS.

While Cmd+V and right-click paste worked perfectly, clicking **Edit -> Paste** from the macOS system menu bar did…nothing. Even worse, digging deeper revealed that this wasn’t just a paste problem. The entire native **Edit menu** (Copy, Cut, Paste, Select All) was completely disconnected from Flutter’s app logic.

Flutter provided a `PlatformMenuBar`, so my first instinct was to override the Paste menu item directly:

```dart
PlatformMenuBar(
  	menus: [
    	PlatformMenu(
      	label: 'Edit',
      	menus: [
        	PlatformMenuItemGroup(
          	members: [
              
            	PlatformMenuItem(
              	label: 'Paste',
              	shortcut: const SingleActivator(
                	LogicalKeyboardKey.keyV,
                	meta: true,
              	),
              	onSelected: _handlePaste,
            	),
          	],
        	),
      	],
    	),
  	],
  	child: ...
)
```

At first glance, this looks reasonable, but it exposes a critical limitation:

Creating a custom `PlatformMenuBar` replaces the native macOS menu entirely. That means:

- Default OS menu items are erased.
- Platform-provided behaviour is lost.
- The menu structure must be manually reconstructed.
- OS changes between macOS versions aren’t preserved.

This wasn’t a new issue as similar reports already existed, but it highlighted a fundamental gap. Flutter does not expose a way to:

- Inspect the existing native macOS menu bar
- Override only specific actions (like Paste)
- Preserve OS-provided defaults that change between macOS versions

On macOS, menu items aren’t just UI; they’re tightly integrated with the responder chain. Simply “handling paste” in Dart isn’t enough if the menu item itself never reaches Flutter.

As Chris put it: “What we need is a macOS plugin that can iterate over what a normal macOS app gets in the menu bar and allows you to override the functionality as you choose.”

That plugin didn’t exist, so I built it.

## Implementation: The Universal Clipboard

Unlike the standard clipboard, `super_clipboard` provides a `ClipboardReader` that can “interrogate” the clipboard. Instead of guessing what’s there, we can explicitly ask: “Can you provide a PNG? A PDF? A File URI?”

I implemented a `_pasteOperation` that prioritises data based on its richness. The logic follows a specific hierarchy:

1. Documents & Files: Check for PDFS, DOCX, XLSX.
2. File URIs: If someone copied a file directly from Finder or File Explorer.
3. Images: Iterating through PNG, JPEG, WebP, etc.
4. Plain/HTML Text: The fallback for standard communication.

This mirrors how users *expect* paste to work: if a file or image is present, paste the file or image; otherwise, paste text.

```dart
final reader = await clipboard.read();
if (reader.canProvide(Formats.fileUri)) {
  // Handle pasted files
}
for (final format in fileFormats) {
  if (reader.canProvide(format)) {
	// Handle binary files
  }
}
for (final format in imageFormats) {
  if (reader.canProvide(format)) {
	// Handle images
  }
}
if (reader.canProvide(Formats.plainText)) {
  // Fallback to text
}
```

Nothing is assumed. Every branch is explicit.

On the web, clipboard access works very differently. Instead of actively reading from the clipboard, browsers deliver paste data via DOM events. `super_clipboard` abstracts this by exposing `ClipboardEvents`, allowing you to register a paste listener once and reuse the same parsing logic.

To keep the API consistent across platforms, I used **conditional exports**:

```dart
export 'paste_helper_stub.dart'
  if (dart.library.js_interop) 'paste_helper_web.dart';
```

On the web, this registers an event listener. On the desktop, it resolves to a no-op stub. This lets the rest of the app call handlePasteWeb() unconditionally, without platform checks scattered throughout the codebase.

At this point, pasting logic was solved everywhere except one place: **Edit -> Paste** in the macOS menu bar.

## The Final Missing Piece: Building mac_menu_bar

The problem wasn’t Flutter, and it wasn’t the clipboard… it was **AppKit**. On macOS, menu items don’t emit keyboard events or text input signals; they invoke **selectors** directly through the AppKit responder chain. My goal was to keep the native menu but “borrow” its actions. To achieve this, I wrote a native macOS plugin in Swift that performs a “surgical intervention” on the app’s main menu. Instead of replacing the menu, the plugin:

1. Locates existing menu items (like “Paste”) using their system selectors.
2. Saves a reference to the original target and action.
3. Injects itself as the new target.

In the native code, I used the findMenuItem helper to crawl the NSApplication.shared.mainMenu . Once the “Paste” item is found, we swap its destination:

```swift
private func overrideMenuItem(selector: Selector, handler: Selector) {
	guard let item = findMenuItem(for: selector) else { return }
   
	// 1. Save the original so we don't break the OS
	originalActions[selector] = OriginalAction(target: item.target as AnyObject?, selector: selector)
   
	// 2. Hijack the action
	item.target = self
	item.action = handler
}
```

One of the most important features of this plugin is the **Boolean Handshake**. When a user clicks “Paste” in the menu:

1. The swift plugin catches the event.
2. It sends a message to Flutter: “Hey, do you want to handle this paste?”
3. If Flutter returns true (e.g because we have an image in the clipboard), the plugin stops there.
4. If Flutter returns false or is null, the plugin calls forwardDefaultAction , which sends the event back to the original macOS handler. This ensures that if our custom logic doesn’t apply, the standard text passing still works perfectly.

```swift
MacMenuBar.onPaste(() async {
  final handled = await myCustomPasteLogic();
  return handled; // True triggers our code, False triggers native OS code
});
```

On the Dart side, I implemented a clean PlatformInterface to make the plugin easy to use. Developers don’t need to know about Swift selectors or NSMenu ; they simply register an asynchronous callback:

```dart
/// Registers a callback to be invoked when the Paste menu item is selected.
///
/// The [handler] should return a [Future] that completes with `true` if the
/// operation was handled, or `false` to allow the default system behavior.
///
/// Example:
/// ```dart
/// MacMenuBar.onPaste(() async {
///   // Handle paste operation
///   return true; // Return true to indicate the action was handled
/// });
/// ```
static void onPaste(Future<bool> Function() handler) =>
    MacMenuBarPlatform.instance.setOnPasteFromMenu(handler);
```

if you'd like to handle macOS menu bar items for your own purposes, you can using [the mac_menu_bar package](https://pub.dev/packages/mac_menu_bar).

## Summary

What began as a single GitHub issue in the `flutter_ai_toolkit` repo resulted in a robust, reusable solution for the entire Flutter community. By digging into the native layer, we solved three major problems:

- **Universal Image Pasting:** Supporting images across Web, Mobile, and Desktop (macOS).
- **Native Menu Interception:** Bridging the gap between the macOS Menu Bar and Flutter.
- **Platform Harmony:** Creating a fallback system that respects the OS while extending its capabilities.

Sometimes, the hardest bugs aren’t where you expect them to be. And sometimes, fixing “paste” means understanding how an operating system really works.