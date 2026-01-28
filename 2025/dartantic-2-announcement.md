---
tags: flutter, ai
---

# Dartantic 2.0: The Nano Banana Edition

<img src="_images/dartantic_2_announcement/nano_banana_bot.jpg" class="main-blog-image" style="width: 250px" />

**tl;dr:** If you're new to dartantic, it's a multi-provider agentic toolkit for Dart and Flutter developers that runs wherever Dart runs, i.e. Flutter web, desktop and mobile, CLI and server-side. Today there's a new release, but you can skip all of that and head to the docs to get all you need to get started: https://docs.dartantic.ai.

## Welcome to Dartantic 2.0!

Are you a Dart or Flutter developer deep into AI looking for a multi-provider agentic framework runs wherever Dart runs? Or perhaps you're simply AI curious?

> In either case, have I got a deal for you: **today is the day that dartantic_ai 2.0 ships!**

This is a big one. 12K+ lines of new and updated code. **Unified thinking** mode. **Server-side tooling** across all of the Big 3 providers: Google, Anthropic and OpenAI. New **media generation models** to create images and files of all kinds. Plus a ton of quality of life improvements. As well as some breaking changes (making omlets and all that).

> And, of course, **Nano Banana and Gemini 3 Pro Preview support.**

What more could any young Dart or Flutter developer ask for? And I've got it all here for you right now. 

## Getting Started

If you're new to Dartantic, here's the 30-second version:

```dart
import 'package:dartantic_ai/dartantic_ai.dart';

void main() async {
  // Create an agent - use the default model or specify one
  final agent = Agent('google:gemini-3-pro-preview');

  // Send a message
  final result = await agent.send('Hello! What can you help me with?');
  print(result.output);
}
```

That's it. Set your API key in the environment (`OPENAI_API_KEY`, `GOOGLE_API_KEY`, etc.) and you're off. Want to switch providers? Change `google` to `anthropic` or `openai-responses`. Want a different model? Just change the model part of the string. For the full details, you've got [the dartantic docs](https://docs.dartantic.ai).

### Unified Thinking API

Extended thinking (chain-of-thought reasoning) is now a first-class feature in Dartantic with a simplified, unified API across all providers.

Here's what it looks like:

```dart
// Enable thinking
final agent = Agent('google:gemini-3-pro-preview', enableThinking: true);

// Access thinking as a first-class field
final result = await agent.send('Complex question...');
if( result.thinking != null) print(result.thinking);
```

This new model works the same for whatever provider you're using (assuming they support thinking). The provider-specific fine-tuning options remain for advanced use cases:

- `GoogleChatModelOptions.thinkingBudgetTokens`
- `AnthropicChatOptions.thinkingBudgetTokens`
- `OpenAIResponsesChatModelOptions.reasoningSummary`

But for most of us? Just flip the boolean and go.

### Server-Side Tools Across Providers

Server-side tools are now supported across multiple providers. These are tools that run on the provider's infrastructure, not yours.

| Provider             | Tools Available                                             |
| -------------------- | ----------------------------------------------------------- |
| **OpenAI Responses** | Web Search, File Search, Image Generation, Code Interpreter |
| **Google**           | Google Search (Grounding), Code Execution                   |
| **Anthropic**        | Web Search, Web Fetch, Code Interpreter                     |

Here's how you use them:

```dart
// Google server-side google search
final agent = Agent(
  'google',
  chatModelOptions: const GoogleChatModelOptions(
    serverSideTools: {GoogleServerSideTool.googleSearch},
  ),
);

// Anthropic server-side web search
final agent = Agent(
  'anthropic',
  chatModelOptions: const AnthropicChatOptions(
    serverSideTools: {AnthropicServerSideTool.webSearch},
  ),
);

// OpenAi server-side web search
final agent = Agent(
  'openai-responses',
  chatModelOptions: const OpenAIResponsesChatModelOptions(
    serverSideTools: {OpenAIServerSideTool.webSearch},
  ),
);

```

The pattern is consistent across providers even though the underlying implementations are completely different. That's the whole point of dartantic. I hate to say "write once, run on any provider" but...

I'm also keeping my eye on [Google's file search tool](https://github.com/googleapis/google-cloud-dart/issues/94) which would bring Google to feature parity with OpenAI's vector search capabilities. As soon as that lands in the Dart SDK, dartantic will support it.

### Media Generation with Nano Banana Pro

If you're into LLMs at all, you've probably seen talk about Nano Banana and Nano Banana Pro. The new Gemini media generation model supports both:

```dart
// Google provider uses Nano Banana by default (gemini-2.5-flash-image)
// for image generation
final agent = Agent('google');

final imageResult = await agent.generateMedia(
  'Create a b&w drawing of a robot mascot for a developer conference.',
  mimeTypes: const ['image/png'],
);

// Configuring the Google provider with Nano Banana Pro (gemini-3-pro-image-preview)
final agent = Agent('google?media=gemini-3-pro-image-preview');

final imageResult = await agent.generateMedia(
  'Create a 3D robot mascot for a developer conference.',
  mimeTypes: const ['image/png'],
);
```

The image at the top of this blog post was generated by Nano Banana Pro during one of the test runs.

But here's where it gets interesting. The dartantic's media generation isn't limited to images:

```dart
final agent = Agent('google');

// PDF generation - uses Gemini 3 Pro Preview + code execution
final pdfResult = await agent.generateMedia(
  'Create a one-page PDF with the title "Project Status" and '
  'three bullet points summarizing a software project.',
  mimeTypes: const ['application/pdf'],
);

// CSV generation - uses Gemini 3 Pro Preview + code execution
final csvResult = await agent.generateMedia(
  'Create a CSV file with columns: date, users, revenue. '
  'Add 5 rows of sample data.',
  mimeTypes: const ['text/csv'],
);
```

The media generation models (Google, Anthropic and OpenAI via the responses API) are implemented to route to their image generation if they have one and to their code execution environment if they don't. For you, pick your provider, send in the prompt + mime type and you're good to go.

## Filling the Gaps

As I build out dartantic, I get to find out each provider's "special" behavior.

**Structured Output + Tools**: For example, all of the Big 3 support tool calling and structured output. However, only OpenAI (via either the completions or responses APIs) supports tool calling AND structured output in the same request. Neither Google nor Anthropic do. So, [inspired by the community](https://github.com/csells/dartantic_ai/issues/32) (thanks @fatherOfLegends!), I've worked around that problem for both the Google and Anthropic providers so you can just do this and good things happen:

```dart
class TimeAndTemperature {
  const TimeAndTemperature({required this.time, required this.temperature});
  factory TimeAndTemperature.fromJson(Map<String, dynamic> json) => ...
  static final schema = ...

  final DateTime time;
  final double temperature;
}

final provider = Agent('google'), // or openai or anthropic or ...
  tools: [temperatureTool],
);

final result = await agent.sendFor<TimeAndTemperature>(
  'What is the time and temperature in Portland, OR?',
  outputSchema: TimeAndTemperature.schema,
  outputFromJson: TimeAndTemperature.fromJson,
);

print('time: ${result.output.time}');
print('temperature: ${result.output.temperature}');
```

I keep an eye out for provider improvements so as the LLMs get better, dartantic gets better, too.

**Google Native JSON Schema**: For example, Google's Gemini API now uses native JSON Schema support via `responseJsonSchema` instead of the custom Schema object conversion. This is an internal change with no API surface changes for you, except that now you can pass in much more interesting JSON schemas - including `anyOf`, `$ref`, and other JSON Schema features that weren't previously supported.

## Quality of Life

I've also made some smaller improvements based on real-world user feedback. [Keep those cards and letters coming!](https://github.com/csells/dartantic_ai/issues)

### Custom Headers

Real-world enterprise deployments often need to pass custom headers to API calls - for authentication proxies, request tracing, compliance logging, you name it. For those cases, dartantic 2.0 adds custom header:

```dart
final provider = GoogleProvider(
  apiKey: apiKey,
  headers: {
    'X-Request-ID': requestId,
    'X-Tenant-ID': tenantId,
  },
);
```

This has been plumbed through all of the providers: OpenAI, Google, Anthropic, Mistral, and Ollama. The headers flow through to all API calls, and custom headers can even override internal headers when needed.

### Google Function Calling Mode

Also, in case you'd like to control just hard hard you push on Gemini using the tools you pass in, I added `functionCallingMode` and `allowedFunctionNames` properties to `GoogleChatModelOptions`:

```dart
final agent = Agent(
  'google',
  chatModelOptions: GoogleChatModelOptions(
    functionCallingMode: GoogleFunctionCallingMode.any, // Force tool calls
    allowedFunctionNames: ['get_weather'], // Limit to specific functions
  ),
  tools: ...
);
```

Available modes:

- `auto` (default): Model decides when to call functions
- `any`: Model always calls a function
- `none`: Model never calls functions
- `validated`: Like auto but validates calls with constrained decoding

## Breaking Changes

I took this opportunity in the major version bump to break some things that have been bothering me.

### Simplified Provider Lookup

I removed static provider instances, e.g. `Providers.google`, as being not useful in practice. Either you want the default initialization for a project and the convenience of using a model string, e.g. `Agent('claude')`, or you want to use the type and create a provider instance with non-defaults, e.g. `OpenAIProvider('openai-responses:gpt-5', apiKey: ...)`. The halfway of having a typed default instance was good for discovery, but if you're using syntax completion to choose your LLM, now you've got two problems. :)

```dart
// OLD
final provider = Providers.openai;

// NEW
final provider1 = OpenAIProvider();
```

Once I removed the static instances, there was no need for an entire type just to look up providers, so I moved that to `Agent` instead. Also, providers are now created via factory functions, not cached instances.

```dart
// OLD
final provider = Providers.get('openai');
final allProviders = Providers.all;
Providers.providerMap['custom'] = MyProvider();

// NEW
final provider = Agent.getProvider('openai');
final allProviders = Agent.allProviders;
Agent.providerFactories['custom'] = MyProvider.new;
```

Custom providers can be plugged into the new `Agent.providersFactories` map, so named-based lookup works just like built-in providers.

### Removed ProviderCaps

I added `ProviderCaps` originally to help users drill in on what providers they could use in their apps. However, it really became "what are the capabilities of the default model of that provider" because every model on every provider is different and cannot be captured with one enum. It's still useful for driving tests, so I moved it into the tests and took it out of the provider interface as misleading.

```dart
// OLD
final visionProviders = Providers.allWith({ProviderCaps.chatVision});

// NEW
// use Provider.listModels() and choose via ModelInfo instead
```

For runtime capability discovery, use `Provider.listModels()` instead - it gives you more accurate per-model information.

### Removed Flakey Instrinsic Providers

There are lots and lots of OpenAI-compatible providers in the world, so trying to test Dartantic against all of them is impractical. Plus, most of them don't do such a great job of actually implementing the features, e.g. multi-turn tool calling.

So, I've removed three of them from the list of built-in providers (Together, Google OpenAI-compat, and Ollama OpenAI-compat) and moved them to the `openai_compat.dart` example. You can still use them and define them in your app - in fact, they can be configured to work exactly like the built-in providers using the new `Agent.providerFactories` - but they're not built in and they're no longer part of the Dartantic testing suite.

I did leave the Open Router provider as built-in via `Agent('openrouter')` since it's so popular and they do a good job of implementing the API across their models.

### Exposing dartantic_interface from dartantic_ai

The dartantic_interface package is great for building your own providers without pulling in all of Dartantic. However, the way I had it split meant that you had to import both packages into every file that used them both. No more!

```dart
// OLD - had to import both packages
import 'package:dartantic_ai/dartantic_ai.dart';
import 'package:dartantic_interface/dartantic_interface.dart';

// NEW - one import does it all
import 'package:dartantic_ai/dartantic_ai.dart';
```

## What's Next?

I'm continuing to track the LLM provider landscape and add support for new features as they become available. I've certainly got plenty on [my list](https://github.com/csells/dartantic_ai/issues) to do. : )

If you run into issues or have feature requests, please [open an issue on GitHub](https://github.com/csells/dartantic_ai/issues). And if you build something cool with Dartantic, let me know! I'd love to hear about it.

You can get the details here:

- **Documentation**: [docs.dartantic.ai](https://docs.dartantic.ai)
- **Package**: [pub.dev/packages/dartantic_ai](https://pub.dev/packages/dartantic_ai)
- **Source**: [github.com/csells/dartantic_ai](https://github.com/csells/dartantic_ai)
- **Migration Guide**: [docs.dartantic.ai/migration-guide](https://docs.dartantic.ai/migration-guide)

Enjoy!