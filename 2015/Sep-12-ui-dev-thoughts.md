Tags: spout

Choose HTML for UI Development
==============================
<img style="float: right; padding: 5px;" src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/61/HTML5_logo_and_wordmark.svg/120px-HTML5_logo_and_wordmark.svg.png" />

On Sept. 10, 2015, [Winston Kodogo writes](</13741#comment-2245753814>):

>   Hey Chris, if you're feeling happy enough to blog, how about a post giving
>   us your current thoughts on UI development. A friend of mine has asked me
>   for advice on moving an in-house app from VB6 to something he can find
>   people to modify if he needs to, and I can't for the life of me think of
>   what to tell him. I have your most excellent books on Windows Forms and WPF,
>   but what would the modern (!) equivalent be. Not WebJS, surely.

Thanks for the softball question, Winston. I do love to pontificate
extemporaneously (although I have already given away the ending).

Pre-Windows
===========

I started my Windows career as a [Petzoldian Windows
developer](<http://www.amazon.com/Programming-Windows-3-1-Charles-Petzold/dp/1556153953>),
but before that, I developed UIs in
<a href="https://en.wikipedia.org/wiki/Curses_(programming_library)">curses</a>, <a href="https://en.wikipedia.org/wiki/Flavors_(programming_language)">Flavors
Lisp</a> and even
voice-based UIs in a proprietary AT&T scripting language for which I no longer
remember the name.

Windows
=======

As a Windows developer, I programmed against 16 and 32-bit
[GDI](<https://en.wikipedia.org/wiki/Graphics_Device_Interface>) and
[User](<https://en.wikipedia.org/wiki/Windows_USER>),
[GDI+](<https://msdn.microsoft.com/en-us/library/windows/desktop/ms533798.aspx>),
[MFC](<https://en.wikipedia.org/wiki/Microsoft_Foundation_Class_Library>),
[WTL](<https://en.wikipedia.org/wiki/Windows_Template_Library>), [Windows
Forms](<https://en.wikipedia.org/wiki/Windows_Forms>),
[WPF](<https://en.wikipedia.org/wiki/Windows_Presentation_Foundation>),
[WinJS](<https://dev.windows.com/en-us/develop/winjs>),
[Silverlight](<https://en.wikipedia.org/wiki/Microsoft_Silverlight>) and
[Xamarin Forms](<http://xamarin.com/forms>). I've even written [books, articles
and presentations](</writing>) about many of these toolkits.

And even now, as I type this, I'm using a different framework,
[Markdown](<http://daringfireball.net/projects/markdown/>), to produce the UI
you’re using now.

Transcend Windows
=================

It's this last fact that leads me to my pretty much universal recommendation for
UI development: The UI framework with the most reach, the best tools, the most
community support and the best staying power is, of course, HTML, with it's
kissing cousins, JavaScript and CSS.

There are good reasons to choose others, of course:

-   Are you building a **desktop game**? Use [OpenGL](<https://www.opengl.org/>)
    or [DirectX](<https://en.wikipedia.org/wiki/DirectX>).

-   Are you building a **mobile game**? Use the iOS or Android APIs or, even
    better, [Unity](<https://unity3d.com/>).

-   **Do you love C\# and .NET**? Then some implementation of
    [XAML](<https://en.wikipedia.org/wiki/Extensible_Application_Markup_Language>)
    may suit your needs.

-   Are you targeting developers with **automation** or specific integration
    needs? Then a command line interface is probably what you want.

But other than that, **my default UI development advice is always always HTML**.

HTML How
========

The beauty of an HTML-based UI framework is that it has grown steadily in
capability, usage and ubiquity since it was introduced in 1993. It's hard to
find an environment where HTML doesn't run:

-   **Desktop Browser: **This is where HTML was first shown and it arguably
    shows best here. There's little you can't do in this environment, including
    near native games using [WebGL, Enscripten and
    asm.js](<https://blog.mozilla.org/blog/2014/03/12/mozilla-and-epic-preview-unreal-engine-4-running-in-firefox/>)
    (soon to become
    [WebAssembly](<https://brendaneich.com/2015/06/from-asm-js-to-webassembly/>)).

    This is probably your best bet for any kind of internal app, e.g. the kind
    of thing that VB6 and WinForms are usually used for. Toolkits like
    [Angular](<https://angularjs.org/>) and
    [React](<http://facebook.github.io/react/>) are popular for desktop browser
    apps because they provide the end-to-end developer story while still having
    rich extension libraries.

    That said, if I were going to tackle a new web site or web app today, I’d
    probably use [WebComponents](<http://webcomponents.org/>) (and
    [Polymer](<https://www.polymer-project.org/>)) for the excellent reasons
    that Joe Gregorio lays out in [his OSCON 2015
    talk](<https://www.youtube.com/watch?v=GMWAHzXQnNM>).

-   **Mobile Browser: **Today’s smartphone-based browsers are very capable and
    adapt to desktop-based web sites fairly well. However, I do recommend making
    sure your desktop web sites work well on mobile as well, if for no other
    reason than you want to make sure you don’t lose your ranking on Google,
    which now prefers mobile-friendly sites for mobile search results (and
    provides [a tool to help you get your site
    ready](<https://www.google.com/webmasters/tools/mobile-friendly/>)).

    With the modern enterprise moving towards mobile as fast now as they have
    been moving towards the web-based intranet over the last decade, I’d
    recommend making sure that your internal web sites/apps work well on mobile
    if you want to avoid the CTO stopping by your desk in the near future.

-   **Desktop Stand-alone App: **The new trend (with companies like
    [GitHub](<https://desktop.github.com/>) and
    [Microsoft](<https://code.visualstudio.com/>) on board) for building
    cross-platform, stand-alone desktop apps is to use HTML, but package it into
    an app, specifically using something like
    [Electron](<http://electron.atom.io/>). This is the new hotness, but this is
    what I’d do if I wanted to build a stand-alone desktop app today.

-   **Mobile Store App: **Most mobile apps today are built for the iOS and
    Google Play stores, although increasingly enterprises are building mobile
    apps for internal use and distributing them internally using private stores
    like [Appaloosa](<https://www.appaloosa-store.com/>).

    Today, mobile apps are built overwelminging using the platform-specific
    native APIs, most often with two separate teams for each app, one for iOS
    and one for Android. However, as mobile becomes more of an enterprise
    platform, you want apps that are developed quickly, can be maintained easily
    and can be built with a smaller number of developers, which all boils down
    to one thing: you want to have a single codebase for your internal mobile
    apps instead of one codebase per platform.

    Today, you have two viable alternatives when building mobile apps that
    target multiple platforms from a single source code base:
    [Xamarin](<https://xamarin.com/platform>) and [Adobe
    PhoneGap](<http://phonegap.com/>). Xamarin is essentially cross-platform
    .NET with some UI controls and native compilation thrown in, whereas
    PhoneGap is a framework for hosting HTML on mobile platforms (as well as
    others, but no one does that) along with a platform-agnostic JavaScript API
    that maps to native functionality.

    I've used both of these frameworks and they both have their pros and cons.
    Xamarin has a simple UI framework that changes it’s look ’n’ feel depending
    on the platform your app is running on, but PhoneGap does not. In that case,
    I’d also bring [Ionic](<http://ionicframework.com/>) into the mix, which
    will let you write your app in HTML but give you the native UI as
    appropriate (and a beautiful one at that).

    There has been much said about whether HTML is appropriate for building
    mobile apps and while it’s possible to build [a bad
    app](<https://www.facebook.com/notes/facebook-engineering/under-the-hood-rebuilding-facebook-for-ios/10151036091753920>)
    using any platform, in my experience, it’s not hard to get [an app that’s
    built in HTML to look ’n’ feel just like a native
    app](<https://www.sencha.com/blog/the-making-of-fastbook-an-html5-love-story/>).
    When I did it, there were some tricks involved, but frameworks like Ionic
    pretty much take away the need for that these days. Of course there are
    always going to be apps that can’t be built this way, but as a percentage
    (leaving out twitch games), that number is getting smaller and smaller.

Where are we?
=============

Of course, the best and worst part of the web platform is that the community
changes it’s mind about how sites and apps should be built for the web platform
about as often as I change my underwear, so the specific recommendations are
merely appropriate for this point in time. The general guidance remains the
same, however: **choose HTML for your UI development** unless you’ve got a darn
good reason not to.
