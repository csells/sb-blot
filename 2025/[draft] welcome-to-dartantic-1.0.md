---
tags: flutter, ai
---

# Welcome to dartantic_ai 1.0!

<img src="_images/welcome-to-dartantic-1.0/gemini-dartantic-logo.jpg" class="main-blog-image" style="width: 250px" />

Dartantic is an agentic framework designed to make building client and server-side apps in Dart with generative AI easier and more fun!

It works across providers (Google, OpenAI, Anthropic, etc) and runs anywhere your Dart code runs (Flutter desktop, Flutter mobile, Flutter web, CLI, server).

It allows you to write code like this:

```dart
// Tools that work together
final tools = [
  Tool(
    name: 'get_current_time',
    description: 'Get the current date and time',
    onCall: (_) async => {'result': DateTime.now().toIso8601String()},
  ),
  Tool(
    name: 'find_events',
    description: 'Find events for a date',
    inputSchema: JsonSchema.object({
      'date': JsonSchema.string(),
    }),
    onCall: (args) async => ..., // find events
  ),
];

// Agent chains tools automatically, no matte what provider you're using,
// e.g. openai, google, openrouter or your custom provider. And if you want to
// specify the model, you can, e.g. "openai:gpt-4o", "google:gemini-2.5-flash" or
// "your-provider:your-model".
final agent = Agent('openai', tools: tools);
final result = await agent.send('What events do I have today?');

// Agent will:
// 1. Call get_current_time to figure out what "today" means
// 2. Extract date from response
// 3. Call find_events with that date
// 4. Return final answer with events
```

I had all of that working with Gemini and OpenAI LLMs three weeks ago. I just needed to add support for a few more providers and I'd be ready for a 1.0. So I did what anyone would do: I spent three weeks rebuilding dartantic from first principles.

## Building on langchain_dart

It was three weeks ago when I first really dove into [the most excellent langchain_dart repo](https://github.com/davidmigloz/langchain_dart) from [David Miguel Lozano](https://github.com/davidmigloz). And when I did, I discovered that he was way ahead of me with features AND providers. There was a lot of Langchain stuff in there of course -- David had been very thorough -- but it also had a lovely compatibility layer over the set of LLM provider-specific Dart SDK packages (which David also built and maintained). So, on the day after a launched dartanic 0.9.7 at FlutterCon in New York, I sat down with Claude Code and carved my way into David's Langchain implementation, chipping away until I had extracted that compat-layer.

And on top of that, I built dartantic_ai 1.0.

As you can see from the most [epic CHANGELOG ever](https://pub.dev/packages/dartantic_ai/changelog#100), I learned a ton from David along the way, including:

- to use Dart types for typed output on the `Agent.sendFor<TOutput>` method instead of on the `Agent` itself so each LLM response can have it's own type
- to use Dart types for typed input on tool calls on the parameterized `Tool<TInput>` type itself
- to use a parameterized model options parameter so each model can be created in a generic way, but also support provider-specific typed model options
- to expose a set of static provider instances, e.g. `Providers.openai`, `Providers.anthropic`, etc. to make it easy to just grab one without using string names if you don't want to
- to expose usage tracking
- to handle embeddings in chunks
- and so many other tiny details that just makes dartantic better!

David's langchain base allowed me to build support for 11x providers, 5x native (Mistral, Anthropic, Google, OpenAI and Ollama) and 6x more OpenAI-compatible configurations (Together, Cohere and Lambda as well as Ollama and Google configurations for their OpenAI-compatible endpoints). All 11x providers handle chat and 5x of them handle embeddings. I started with more OpenAI-compatible configurations, but their implementations were either so weak or so flakey or so both (I'm looking at you, Nvidia) that I dropped them -- they couldn't pass the more than 1100 tests I built out to test dartantic's support for them. But feel free to drop in your own!

## Industrial Strength

On top of David's langchain work, I then built out a lot of new features for dartantic, including:

- custom providers that participate in the named lookup just like the built-in providers
- typed output
- typed tool input
- typed ouput WITH tool calls WITH streaming ([progressive JSON rendering](https://github.com/csells/partial_json_expander) anyone?)
- multi-provider chat-compatible message format
- thorough logging w/ easy setup and filtering
- usage tracking
- and more...

You can see the nitty gritty in [the dartantic docs](https://docs.dartantic.ai).

# What's Next

I've separated out [the core dartantic interfaces](https://github.com/csells/dartantic_ai/tree/main/packages/dartantic_interface) so that you can build a dartantic provider without depending on all of dartantic and so that I can make sure that dartantic continues to run everywhere that Dart runs. I'm working with [the nice folks at Cactus](https://cactuscompute.com/) to get their enterprise-grade local mobile-device-optimized LLMs into dartantic as a custom provider. I also want to [get a provider for firebase_ai in there](https://github.com/csells/dartantic_ai/issues/11) for my Flutter peeps who don't want to mess with API keys in their client apps.

Of course, anyone that wants to can build a dartantic provider. Let me know if you do! I'd love to track them in the docs.

I also have plans to support [image generation](https://github.com/csells/dartantic_ai/issues/10) and [audio transcription](https://github.com/csells/dartantic_ai/issues/9), as well as the new [OpenAI Responses API](https://platform.openai.com/docs/api-reference/responses) and context caching to reduce token usage.

And I have [big dreams for a dartantic builder](https://github.com/csells/dartantic_ai/issues/30) that translates Dart types into JSON serialization and JSON schema for you automatically, streamlining the agent creation considerably:

```dart
@Agent()
class TacAgent extends Agent {
  TacAgent(super.model);

  @Run()
  Future<TownAndCountry> run(String prompt) => _$TownAndCountryAgentRun(prompt);
  
  @Tool()
  Future<DateTime> getCurrentDateTime() => DateTime.now();
}
```

I'm tracking [my ideas for the future of dartantic](https://github.com/csells/dartantic_ai/issues?q=sort%3Aupdated-desc+is%3Aissue+is%3Aopen) on GitHub. Feel free to add your own.

## Where Are We

My goal with dartantic isn't for me to be a one-man band. The idea is that dartantic can grow with the AI needs of the Dart and Flutter community, while maintaining it's principles of multi-provider support, multi-platform support and fun!

Want to steer where dartantic goes? Hate something and want it fixed? Get involved! Here's how:
- **Package**: [pub.dev/packages/dartantic_ai](https://pub.dev/packages/dartantic_ai)
- **Docs**: [docs.dartantic.ai](https://docs.dartantic.ai)
- **Code**: [github.com/csells/dartantic_ai](https://github.com/csells/dartantic_ai)
- **Discussion Forum**: [github.com/csells/dartantic_ai/discussions](https://github.com/csells/dartantic_ai/discussions)
- **Issues**: [github.com/csells/dartantic_ai/issues](https://github.com/csells/dartantic_ai/issues)
- **PRs**: [github.com/csells/dartantic_ai/pulls](https://github.com/csells/dartantic_ai/pulls)

If you're building AI-powered apps in Dart or Flutter, give dartantic a try. Switch between providers. Use typed output. Make tool calls. Build agents. Break things. Swear at it. Then come tell me what went wrong.

Welcome to dartantic 1.0. Let's go break some stuff together.
