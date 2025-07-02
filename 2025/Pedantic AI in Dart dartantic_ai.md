Tags: flutter

# Pedantic AI in Dart: [dartantic_ai](https://pub.dev/packages/dartantic_ai)

<img src="_images/voxel-dash.png" class="main-blog-image" style="width: 250px" />

The Python community has a library called [pydantic](https://docs.pydantic.dev/) that adds type checking at run-time to a dynamically typed language. The library allows them to be "pedantic" about type validation in Python aka pydantic; get it? : )

We don't need that for Dart. We have static type checking and it's wonderful.

## Pedantic AI in Python: pydantic-ai

On top of pydantic, the Python community has built [pydantic-ai](https://ai.pydantic.dev/), which makes it easy for you to specify typed output from your LLM requests and to describe typed access to your tools. For example:

```python
# Python example with support for multiple models
import os

from pydantic import BaseModel
from pydantic_ai import Agent

class TownAndCountry(BaseModel):
    town: str
    country: str

model = 'openai:gpt-4o' # or 'google-gla:gemini-2.0-flash' or ...
print(f'Using model: {model}')
agent = Agent(model, output_type=TownAndCountry)

if __name__ == '__main__':
    result = await agent.run('The windy city in the US of A.')
    print(result.output) // Output: town='Chicago' country='United States'
```

Check out the definition of the `TownAndCountry` type and the use of it when creating an `Agent` object with the `output_type` parameter. That's all you need to get an instance of `TownAndCountry` populated by the LLM based on the prompt.

**Now that's something we don't have in Dart!** Instead, we have to do something like this:

```dart
// Dart example for Gemini only
void main() async {
  final model = gemini.GenerativeModel(
    apiKey: Platform.environment['GEMINI_API_KEY']!,
    model: 'gemini-2.0-flash',
    generationConfig: gemini.GenerationConfig(
      responseMimeType: 'application/json',
      responseSchema: gemini.Schema.object(
        properties: {
          'town': gemini.Schema.string(),
          'country': gemini.Schema.string(),
        },
        requiredProperties: ['town', 'country'],
      ),
    ),
  );

  final result = await model.generateContent([
    gemini.Content.text('The windy city of the US of A.'),
  ]);

  final json = jsonDecode(result.text!);
  final obj = TownAndCountry.fromJson(json);
  print(obj); // Output: TownAndCountry(town: Chicago, country: United States)
}
```

Plus, while the above code works for [the Gemini SDK for Dart](https://pub.dev/packages/google_generative_ai), if I want to do the same thing using the [OpenAI SDK for Dart](https://pub.dev/packages/openai_dart), I have to write very different code:

```dart
// Dart example for OpenAI only
void main() async {
  final client = openai.OpenAIClient(
    apiKey: Platform.environment['OPENAI_API_KEY'],
  );

  final response = await client.createChatCompletion(
    request: const openai.CreateChatCompletionRequest(
      model: openai.ChatCompletionModel.modelId('gpt-4o'),
      responseFormat: openai.ResponseFormat.jsonObject(),
      messages: [
        openai.ChatCompletionMessage.system(
          content:
              'Respond ONLY with JSON containing keys "town" and "country".',
        ),
        openai.ChatCompletionMessage.user(
          content: openai.ChatCompletionUserMessageContent.string(
            'The windy city of the US of A.',
          ),
        ),
      ],
    ),
  );

  final data =
      jsonDecode(response.choices.first.message.content!)
          as Map<String, dynamic>;

  final result = TownAndCountry.fromJson(data);
  print(result); // Output: TownAndCountry(town: Chicago, country: United States)
}
```

There must be a better way!

## A Better Way: dartantic_ai

I was inspired by pydantic-ai for two main features: 

1. An easy way to go between models using just a string descriptor, e.g. `openai:gpt-4o`
2. A common way to provide type information for output and tool calls, i.e. JSON schema

Those are the features I focused on initially for [dartantic_ai](https://pub.dev/packages/dartantic_ai), allowing you to write code like the following:

```dart
// Dart example with support for multiple models
class TownAndCountry {
  TownAndCountry({required this.town, required this.country});
  final String town;
  final String country;  
  
  factory TownAndCountry.fromJson(Map<String, dynamic> json) => TownAndCountry(
      town: json['town'],
      country: json['country'],
    );
  
  static Map<String, dynamic> get schemaMap => {
    'type': 'object',
    'properties': {
      'town': {'type': 'string'},
      'country': {'type': 'string'},
    },
    'required': ['town', 'country'],
    'additionalProperties': false,
  };
  
  @override
  String toString() => 'TownAndCountry(town: $town, country: $country)';
}

void main() async {
  final agent = Agent(
    model: 'openai:gpt-4o', // or 'google:gemini-2.0-flash' or ...
    outputType: TownAndCountry.schemaMap,
  );

  final result = await agent.run('The windy city in the US of A.');
  final obj = TownAndCountry.fromJson(jsonDecode(result.output));
  print(obj); // Output: TownAndCountry(town: Chicago, country: United States)
}
```

Here we've created a class to hold the typed output from the agent, passing in hand-written JSON schema and JSON decoder functions. Already, this is much simpler code than either of the Gemini or the OpenAI samples and it works either family of models by simply changing the model description string.

Further, with a little bit of Dart builder magic, you can use json_serializable and soti_schema to generate the JSON serialization and JSON schema for you:

```dart
// Automatic JSON decoding and schema generation
@SotiSchema()
@JsonSerializable()
class TownAndCountry {
  TownAndCountry({required this.town, required this.country});

  factory TownAndCountry.fromJson(Map<String, dynamic> json) =>
      _$TownAndCountryFromJson(json);

  final String town;
  final String country;

  Map<String, dynamic> toJson() => _$TownAndCountryToJson(this);

  @jsonSchema
  static Map<String, dynamic> get schemaMap => _$TownAndCountrySchemaMap;

  @override
  String toString() => 'TownAndCountry(town: $town, country: $country)';
}

void main() async {
  final agent = Agent(
    model: 'openai:gpt-4o'
    outputType: TownAndCountry.schemaMap,
    outputFromJson: TownAndCountry.fromJson,
  );

  final result = await agent.runFor<TownAndCountry>(
    'The windy city in the US of A.',
  );

  print(result.output); // Output: TownAndCountry(town: Chicago, country: United States)
}
```

Using the builder, we no longer have to write the JSON serialization code or the JSON schema by hand -- json_serialization and soti_schema handle that. And, for fun, we're calling the `runFor<T>` method so that the output you get is typed w/o you having to manually call `jsonDecode`. Magic!

## Potential Future

Right now, we're in "phrase 1" of dartantic_ai development -- building out the core set of features and providers that work with those features (starting with Gemini and OpenAI). That's what the code samples above are all about -- what's the best developer experience we can provide for a working Dart developer adding generative AI to their apps?

Once there's a solid foundation, we can start experimenting with a builder that would allow you to write even simpler code:

```dart
@Agent()
class TacAgent extends Agent {
  TacAgent(super.model);

  @Run()
  Future<TownAndCountry> run(String prompt) => _$TownAndCountryAgentRun(prompt);
}

void main() async {
  final result = await TacAgent('openai:gpt-4o').run('The windy city of the US of A.');
  print(result.output); // Output: TownAndCountry(town: Chicago, country: United States)
}
```

And this is just the beginning. Today, dartantic supports tool calls, which you define with JSON schema in a way that's similar to typed output from a `run` call. Now imagine being able to put a `@Tool` attribute on a method in your agent class and have the tool passed in automatically for you. There are all kinds of possibilities as soon as builders are involved.

## Call for Contributors

As of the writing of this post, I've just started my dartantic_ai journey with [a list of current and pending features](https://pub.dev/packages/dartantic_ai) you can read about on pub.dev. I only support the smallest amount of the Gemini and OpenAI SDK surface area to implement the initial features that are most important to me.

However, pydantic-ai has a big surface area with lots of great stuff for using LLMs in a type-safe, multi-model way that the Dart community would be interested in, including multi-agent support, agent graphs, multi-media support, streaming, etc. I'm going to need help to cover all of that, let alone making it work in a robust, matrix-tested way that can appeal to a growing community of Dart developers dipping their toes into AI.

Is dartantic_ai a style of interacting with LLMs from Dart that appeals to you? Are there features missing that you want or bugs you've found putting it to use? Then have I got a deal for you! Please [contribute issues and PRs](https://github.com/csells/dartantic_ai) and let's get this show on the road!

