---
tags: dart, flutter, ai
---

# Welcome to Dartantic 1.0!

<img src="_images/welcome-to-dartantic-1.0/TODO.png" class="main-blog-image" style="width: 250px" />

TODO: blog post goes here

## Fodder

### Story

I've been heads down for the last three weeks rebuilding dartantic on top of the most excellent model-agnostic framework exposed across the family of langchain_dart packages that David built. Combining that foundation along with the experience I'd gained building it the first time allowed me to expand from 2x chat and embeddings providers to 5x native chat providers (Google, OpenAI, Anthropic, Mistral and Ollama) along with 6x pre-configured OpenAI-compatible providers (Cohere, Together and TODO, along with a configuration of Google and Ollama for their OpenAI-compatible endpoints). It also allowed me to expand to 4x embeddings providers (Google, OpenAI and Mistral natively and Cohere via OpenAI-compatibility).

In addition, David had a lot of other really great ideas in his packages, including a split between chat and embeddings provider, making it easier to manage as well as paving the way for specialized visual and speech models in the future. He also showed me a way to use a generic `Tool<T>` type and a generic `runFor<T>` method, both of which make handling managing automatic JSON => Dart types easier. 

Oh! And while I maintain the `providerName[:/]optional-modelName` named lookup for provider+model resolution,  you can also just use one of the built-in providers like `ChatProvider.google` or `EmbeddingsProvider.openai` if you'd prefer access via direct access. Of course, you can continue to extend the provider namespace with your own custom providers, either creating an agent directly via `ChatAgent.forProvider(AcmeChatProvider())` or via the extensible provider namespace, so `ChatAgent('acme:my-chat-model')` still works, too. For the full list of changes, check out the 1.0 migration guide.

I know that this is a big change, especially moving from the 0.9.7 to 1.0! I wanted a solid foundation for future expansion before moving to the first major release. And we're just getting started! I expect to add a lot to dartantic, including:

- splitting out the base dartantic types to make it easy for folks to build their own providers
- support for vision and audio models
- support for provider-specific message caching so dartantic doesn't send the entire message history if it doesn't need to
- a dartantic builder to make it even easier to use attributes to create a typed agent.
- and [more](https://github.com/csells/dartantic_ai/issues)!

