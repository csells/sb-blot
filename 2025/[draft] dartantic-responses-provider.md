# OpenAI Responses Provider: When Your AI Actually Thinks Out Loud

I don't know about you, but I've spent *way* too many hours staring at AI outputs wondering "what was it *thinking*?" Well, turns out OpenAI had the same thought, because their new Responses API is here to pull back the curtain on model reasoning. And I just couldn't help myself from diving deep into it.

The latest Dartantic AI release (v1.1.0) adds the OpenAI Responses provider—built on the excellent `openai_core` package by @jezell—and it's packed with features that feel like magic. Let me walk you through what makes this special.

## What's the Big Deal?

The OpenAI Responses provider isn't just another chat endpoint. It's OpenAI's answer to the question: "What if AI could actually show its work?" Think of it like this:

- **Thinking metadata streams**: See the model's reasoning as it happens—separate from the final answer
- **Server-side tools**: Web search, code interpreter, image generation, and file search *without hosting anything*
- **Session persistence**: Conversations cached server-side, so you only send new messages each turn
- **GPT-5 Codex access**: Because why not get early access to the latest models?

The whole thing is designed to give you insight into *why* the model said what it said, plus a bunch of built-in capabilities that used to require separate infrastructure.

## Show Me the Code: Basic Setup

Getting started is dead simple. You already know how to create a Dartantic agent, right? Just swap the provider name:

```dart
// Your standard OpenAI provider
final oldAgent = Agent('openai');

// The new hotness
final newAgent = Agent('openai-responses');
```

That's it. Same API key (`OPENAI_API_KEY`), same API surface. Everything just works.

## Thinking Metadata: AI's Inner Monologue

Here's where it gets interesting. The Responses provider can stream the model's reasoning *separately* from its final answer. Perfect for showing users "the AI is working on it" without polluting your chat transcript.

```dart
final agent = Agent(
  'openai-responses:gpt-5',
  chatModelOptions: const OpenAIResponsesChatModelOptions(
    reasoningSummary: OpenAIReasoningSummary.detailed,
  ),
);

await for (final chunk in agent.sendStream('In one sentence: how does quicksort work?')) {
  final thinking = chunk.metadata['thinking'] as String?;
  if (thinking != null && thinking.isNotEmpty) {
    stdout.write('[[${thinking.trim()}]]');
  }
  stdout.write(chunk.output);
}
```

Notice what's happening here: the thinking text shows up in `chunk.metadata['thinking']`, *not* in the message content. That means it:

1. Streams in real-time (you can show a "thought bubble" in your UI)
2. Never gets sent back to the model (keeps token costs down)
3. Stays completely separate from the conversation history

You get the insights without the noise. That's just magic!

## Server-Side Tools: The Infrastructure You Don't Have to Build

Remember when you had to spin up separate services for web search, code execution, or image generation? Yeah, the Responses provider says "forget all that." It comes with four built-in server-side tools that run on OpenAI's infrastructure.

### Web Search

```dart
final agent = Agent(
  'openai-responses',
  chatModelOptions: const OpenAIResponsesChatModelOptions(
    serverSideTools: {OpenAIServerSideTool.webSearch},
    webSearchConfig: const WebSearchConfig(
      contextSize: WebSearchContextSize.high,
      followupQuestions: true,
      location: WebSearchLocation(city: 'Portland', region: 'OR'),
    ),
  ),
);

await for (final chunk in agent.sendStream('Latest Dart language news?')) {
  stdout.write(chunk.output);

  final events = chunk.metadata['web_search'] as List?;
  if (events != null) {
    for (final event in events) {
      stdout.writeln('[search] ${event['type']}');
    }
  }
}
```

The search runs server-side, streams progress events through metadata, and synthesizes results into the response. You can tune context size, request follow-up questions, and even bias results by location.

### Code Interpreter

This one's my favorite. The provider spins up a Python runtime, executes code, and streams back both the execution progress *and* any files it generates.

```dart
final agent = Agent(
  'openai-responses',
  chatModelOptions: const OpenAIResponsesChatModelOptions(
    serverSideTools: {OpenAIServerSideTool.codeInterpreter},
  ),
);

const prompt = 'Calculate the first 10 Fibonacci numbers and save them to a CSV file';
final history = <ChatMessage>[];

await for (final chunk in agent.sendStream(prompt)) {
  stdout.write(chunk.output);
  history.addAll(chunk.messages);

  final events = chunk.metadata['code_interpreter'] as List?;
  if (events != null) {
    for (final event in events) {
      if (event['type'] == 'response.code_interpreter_call_code.delta') {
        stdout.write(event['delta']); // Live code execution!
      }
    }
  }
}

// Generated files appear as DataPart instances in the message
for (final msg in history) {
  for (final part in msg.parts) {
    if (part is DataPart) {
      File('output/${part.name}').writeAsBytesSync(part.bytes);
      print('Saved: ${part.name}'); // e.g., "fibonacci.csv"
    }
  }
}
```

And here's the kicker: containers persist. Grab the `container_id` from the metadata, pass it to the next request, and the model can reuse variables and state:

```dart
String? containerId;
await for (final chunk in agent.sendStream(prompt1)) {
  final cid = containerIdFromMetadata(chunk.metadata);
  if (cid != null) containerId = cid;
}

// Second request reuses the same container
final agent2 = Agent(
  'openai-responses',
  chatModelOptions: OpenAIResponsesChatModelOptions(
    serverSideTools: const {OpenAIServerSideTool.codeInterpreter},
    codeInterpreterConfig: CodeInterpreterConfig(containerId: containerId),
  ),
);
```

That's more than many interview candidates can do with state management, in my experience.

### Image Generation

Want to generate images without leaving the chat flow? Done.

```dart
final agent = Agent(
  'openai-responses',
  chatModelOptions: const OpenAIResponsesChatModelOptions(
    serverSideTools: {OpenAIServerSideTool.imageGeneration},
    imageGenerationConfig: ImageGenerationConfig(
      partialImages: 3,  // Get progressive previews
      quality: ImageGenerationQuality.low,
      size: ImageGenerationSize.square256,
    ),
  ),
);
```

Partial renders stream as base64 in metadata (`metadata['image_generation']`), and the final image arrives as a `DataPart` with proper MIME type. No separate download step.

### File Search (Vector Search)

Upload docs to a vector store, then search them during conversations:

```dart
// Setup (one-time)
final vectorStoreId = await setupVectorStore([
  'docs/architecture.md',
  'docs/api-reference.md',
]);

// Search during chat
final agent = Agent(
  'openai-responses',
  chatModelOptions: OpenAIResponsesChatModelOptions(
    serverSideTools: const {OpenAIServerSideTool.fileSearch},
    fileSearchConfig: FileSearchConfig(
      maxResults: 5,
      vectorStoreIds: [vectorStoreId],
    ),
  ),
);
```

Results stream under `metadata['file_search']` with document filenames, scores, and provider metadata. Perfect for RAG patterns without running your own embeddings pipeline.

## Session Persistence: Prompt Caching on Steroids

Here's a subtle but powerful feature: the Responses provider can cache entire conversations server-side. When you enable `store: true` (the default), Dartantic only sends *new* messages on each turn.

```dart
final agent = Agent(
  'openai-responses',
  chatModelOptions: const OpenAIResponsesChatModelOptions(store: true),
);

var history = <ChatMessage>[];
final r1 = await agent.send('Hello', history: history);
history.addAll(r1.messages);

// Second request only sends "How are you?" + session metadata
final r2 = await agent.send('How are you?', history: history);
```

The orchestrator extracts `_responses_session` from `r1.metadata`, passes it along, and the provider mapper does the rest. Smaller payloads, lower costs, faster responses.

Set `store: false` if you need stateless requests—useful for debugging or when you explicitly want full history in every payload.

## Mixing Server-Side and Custom Tools

And while OpenAI might like you to think their server-side tools are all you need, Dartantic lets you mix them with your own:

```dart
final agent = Agent(
  'openai-responses',
  tools: [myCustomWeatherTool, myCustomDatabaseTool],
  chatModelOptions: const OpenAIResponsesChatModelOptions(
    serverSideTools: {
      OpenAIServerSideTool.webSearch,
      OpenAIServerSideTool.codeInterpreter,
    },
  ),
);
```

The model can call your local tools alongside the provider's hosted ones. Server-side progress streams through metadata, local tool results flow through the normal message pipeline.

## What This Means for Your Apps

Let's talk practical implications, because this isn't just "cool tech for the sake of it":

### 1. **Transparent AI Experiences**
Show users the reasoning process without cluttering the chat. Display thinking metadata in a collapsible panel, log it for debugging, or use it to build trust ("here's why the AI chose this answer").

### 2. **Less Infrastructure to Maintain**
Need web search? File search? Code execution? Just flip a config flag instead of deploying microservices. You still control when and how to use them, but OpenAI handles the runtime.

### 3. **Lower Token Costs**
Session persistence means you're not re-sending entire conversation histories every turn. Combined with thinking metadata staying out of the transcript, your API bills just got lighter.

### 4. **Richer Interactions**
Generate images, execute code, search docs—all in a single conversational flow. The model orchestrates, the provider executes, you collect the results.

## The Catches (Because There Always Are Some)

Not gonna lie, this isn't all sunshine and rainbows:

- **OpenAI-only**: Right now this is exclusive to the Responses API. Other providers might add similar features, but we're not there yet.
- **Less control**: Server-side tools run in OpenAI's environment. You get progress events but can't tweak the runtime or execution sandbox.
- **Metadata isn't standardized**: The `metadata` structure is provider-specific. If you build UI around it, you're coupled to OpenAI's event schema.
- **GPT-5 access varies**: Not everyone has early access to the latest models. Check your account tier.

That said, the architecture is provider-agnostic where it counts. When other providers add thinking metadata or server-side tools, Dartantic's API is ready to light them up.

## Try It Yourself

The [dartantic_ai repo](https://github.com/csells/dartantic_ai) has working examples for everything I've shown here:

- [Thinking metadata](https://github.com/csells/dartantic_ai/blob/main/packages/dartantic_ai/example/bin/thinking.dart) - Streaming reasoning output
- [Web search](https://github.com/csells/dartantic_ai/blob/main/packages/dartantic_ai/example/bin/server_side_tools/server_side_web_search.dart) - Search and synthesize results
- [Code interpreter](https://github.com/csells/dartantic_ai/blob/main/packages/dartantic_ai/example/bin/server_side_tools/server_side_code_interpreter.dart) - Execute Python, reuse containers
- [Image generation](https://github.com/csells/dartantic_ai/blob/main/packages/dartantic_ai/example/bin/server_side_tools/server_side_image_gen.dart) - Stream progressive renders
- [File search](https://github.com/csells/dartantic_ai/blob/main/packages/dartantic_ai/example/bin/server_side_tools/server_side_vector_search.dart) - RAG without the infrastructure

Full docs at [docs.dartantic.ai](https://docs.dartantic.ai).

## What's Next?

I've been experimenting with combining thinking metadata and server-side tools in a single agent—imagine showing the user "I'm searching the web... thinking about the results... generating a visualization..." all streaming live. That kind of transparency makes AI feel less like a black box and more like a collaborative partner.

Let me know if you build something cool with this! I'd love to hear how you use the Responses provider, especially if you find patterns I haven't thought of yet.

---

*Chris Sells
Wizened old coder who still gets excited about new AI toys*
