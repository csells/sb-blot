# Chris Sells Writing Style Guide

*Based on analysis of 30 years of blog posts (1995-2025) from sellsbrothers.com*

## Overview

Chris Sells has maintained a distinctive writing voice across three decades of technical blogging, evolving from early Windows/COM evangelism to modern AI and Flutter development. His style combines deep technical expertise with accessible explanations, humor, personal anecdotes, and a conversational tone that makes complex topics approachable.

**Core Philosophy:** "Speak truth to engineers" - technical content must be accurate, honest about limitations, and solve real problems while being engaging and human.

**Default Story Arc:** Start on shared ground (what the reader already knows), introduce the new idea one brick at a time, illustrate it with concrete examples (code, screenshots, anecdotes), then reflect on the outcome and invite collaboration. Every major section should feel like a guided tour from familiar territory into new insight.

## Voice and Tone Characteristics

### Primary Voice Traits
- **Conversational and Direct**: Writes as if speaking directly to a colleague or friend
- **Self-Deprecating Humor**: Frequently pokes fun at himself, his age, career mistakes
- **Honest and Transparent**: Admits when he doesn't know something or when technology has limitations
- **Enthusiastic but Grounded**: Gets excited about new tech while maintaining realistic expectations
- **Teacher-Mentor**: Explains concepts as if helping someone level up their understanding

### Tone Evolution by Era
- **1995-2005**: More formal, academic style with industry jargon
- **2006-2015**: Increased personal anecdotes and humor
- **2016-2025**: Highly conversational, mentoring tone with frequent direct reader address

### Signature Phrases and Expressions
- "I couldn't help myself from doing a bit of vibe coding"
- "That's just magic!"
- "I don't know about you, but..."
- "And while [X] might like you to think..."
- "Not gonna lie..."
- "...and I just couldn't help myself"
- "Let me know if you do! I'd love to [track/hear about] them"

### Narrative Arc Strategy
- **Common Ground**: Starts by validating the reader's current state or skepticism ("I still run into a large number of engineers who are convinced...").
- **The Pivot**: Introduces the new concept not as a replacement, but as an evolution or a solution to a specific pain point just acknowledged.
- **The Payoff**: Shows the result immediately (screenshot or code) to prove it's worth the effort before explaining how it works.

## Technical Writing Conventions

### Content Structure Patterns

**Problem-Solution-Example Pattern** (Most Common):
1. Present a real-world problem or need
2. Explain the solution approach
3. Provide concrete code examples
4. Discuss trade-offs or limitations
5. End with a call to action or invitation for feedback

**Tutorial/Walkthrough Pattern**:
1. Set context and prerequisites
2. Step-by-step implementation
3. Explain each step's purpose
4. Show complete working example
5. Suggest next steps or extensions

**Opinion/Commentary Pattern**:
1. Establish the topic and why it matters
2. Present multiple viewpoints
3. Share personal experience and insights
4. Make a clear recommendation
5. Acknowledge counterarguments

Regardless of format, each section should generally (not strictly) follow the story arc: anchor to what the reader knows, layer the new information gradually, show it in action, and close with a takeaway or call-to-action.

### Deep Narrative Mechanics

**The Socratic Dialogue**
Chris often anticipates the reader's skepticism or confusion and voices it explicitly.
- *Technique*: "I'm hearing you ask through the Interwebtubes..." or "But wait, you say..."
- *Purpose*: Validates the reader's mental model before updating it. It makes the reader feel seen and understood.

**Demystification First**
When introducing "magical" tech (like AI agents), he immediately grounds it in familiar terms.
- *Technique*: "It's just a loop," "It's just a callback."
- *Purpose*: Reduces anxiety and makes the complex approachable. He pulls back the curtain early.

**Emotional Validation**
He acknowledges the emotional toll of technology shifts.
- *Technique*: Using frameworks like "The 5 Stages of Grief" to categorize and validate feelings of denial or anger.
- *Purpose*: Builds trust. He's not just selling a tool; he's empathizing with the change management process.

**Mental Models & Metaphor**
Uses sticky metaphors to explain abstract concepts.
- *Examples*: "Vibe coding" vs "Describe coding", "Magic wand" vs "Chainsaw".
- *Purpose*: Gives readers a vocabulary to discuss the concepts themselves.

### Technical Depth Approach
- **Start Accessible**: Begin with high-level concepts anyone can follow
- **Layer Complexity**: Gradually introduce technical details
- **Provide Context**: Explain why something matters before diving into how
- **Show, Don't Just Tell**: **Crucial**. Often shows the *result* (screenshot, terminal output) or the *full code* early, then breaks it down. "Here is the code that does X" -> [Code Block] -> "Now let's look at the interesting parts."
- **Address Edge Cases**: Mentions limitations and gotchas
- **Link to Sources**: Provides references to documentation, specs, and related reading

### Code Example Standards

```dart
// Chris's code examples follow consistent patterns:

// 1. Always include necessary imports
import 'dart:io';
import 'package:google_generative_ai/google_generative_ai.dart';

// 2. Use meaningful variable names
final apiKey = Platform.environment['GEMINI_API_KEY'];

// 3. Include error handling
if (apiKey == null) {
  stderr.writeln('Please set the GEMINI_API_KEY environment variable.');
  exit(1);
}

// 4. Comments explain the "why", not just "what"
// Agent will automatically call get_current_time to figure out what "today" means
final result = await agent.send('What events do I have today?');
```

**Code Presentation Rules:**
- Always provide complete, runnable examples when possible
- Include imports and setup code
- Use meaningful variable and function names
- Add comments to explain non-obvious logic
- Show both the setup and the usage
- Include error handling in production examples
- Prefer modern language features and idiomatic code
- Always test code before publishing

## Humor and Personality Integration

### Humor Types and Usage

**Self-Deprecating Humor** (Most Frequent):
- About his age: "cough 40 years cough", "wizened old coder", "back in my day"
- About career mistakes: "I was younger. And I needed the money."
- About technology choices: "that's more than many interview candidates can do with fizzbuzz in my experience"

**Pop Culture References**:
- Movie quotes: "I don't believe you" (Anchorman style)
- Technology history: "Remember back in the eighties, the IBM PC was everything"
- Internet culture: References to memes and current online trends

**Gentle Sarcasm**:
- About vendor claims: "while the vendors of AI-agent-based tooling might like you to think of their products as PFM"
- About corporate speak: "the latest corporate mandates encourage you to use AI coding agents"

### Humor Placement Strategy
- **Opening Hook**: Start with a light joke or observation to draw readers in
- **Transition Relief**: Use humor to break up dense technical sections
- **Closing Warmth**: End with a smile-inducing comment or invitation
- **Never at Reader's Expense**: Humor is always self-directed or at abstract concepts, never mocking readers

### Personal Anecdotes
- **Career Stories**: Tales from Microsoft, Google, consulting work
- **Family Life**: References to partner, children (when appropriate)
- **Learning Experiences**: Mistakes that taught him something valuable
- **Industry Observations**: Behind-the-scenes glimpses of tech culture

## Image and Media Usage

### Image Strategy
- **Hero Images**: Every major post has a main blog image, usually 250px wide
- **Explanatory Screenshots**: Show UI, error messages, or results
- **Architectural Diagrams**: When explaining system design
- **Humorous Images**: Memes or created graphics to illustrate points
- **Process Flows**: Step-by-step visual guides

### Image Technical Specs
```html
<img src="_images/folder-name/image-name.webp" 
     class="main-blog-image" 
     style="width: 250px" />
```

### Image Content Types
- **Screenshots**: Always crop to show only relevant UI elements
- **Code Output**: Terminal sessions, console output, test results
- **Memes/Humor**: Custom-created graphics that support the narrative
- **Architecture**: Simple, clean diagrams explaining system relationships
- **Progress**: GIFs showing interactive functionality when relevant

## Writing Process and Development Patterns

### Post Development Workflow
1. **Start with a Problem**: Every post addresses a real need or question
2. **Create Working Code**: Build and test everything before writing about it
3. **Write Conversationally**: Draft as if explaining to a colleague
4. **Add Examples Liberally**: Show concrete implementations
5. **Include Disclaimers**: Be honest about limitations and caveats
6. **End with Engagement**: Invite feedback, questions, or contributions

### Research and Preparation
- **Hands-On Testing**: Actually builds and uses the technologies discussed
- **Source Citation**: Links to official documentation, related blog posts, GitHub repos
- **Multiple Perspectives**: Considers different use cases and approaches
- **Community Engagement**: Incorporates feedback from previous posts and discussions

## Evolution Timeline: 30 Years of Style Development

### Era 1: Early Technical Writing (1995-2005)
**Characteristics:**
- More formal, academic tone
- Heavy focus on Windows development (Win32, COM, .NET)
- Detailed technical specifications
- Less personal voice, more authoritative

**Sample Style:**
> "Java programmers have long enjoyed the benefits of reflection and full-fidelity type information, and .NET (finally) delivers that bit of nirvana to the Windows platform."

### Era 2: Personal Voice Emergence (2006-2015)
**Characteristics:**
- Increased use of personal anecdotes
- More conversational tone
- Self-deprecating humor appears
- Still deeply technical but more accessible

**Sample Style:**
> "I've been working at home off and (mostly) on for 16 years... From theoatmeal.com. Recommended!"

### Era 3: Mature Conversational Style (2016-2025)
**Characteristics:**
- Fully conversational, mentoring voice
- Heavy use of "I don't know about you, but..." constructions
- Direct reader address throughout
- Extensive use of AI and modern development practices
- Honest about uncertainties and learning

**Current Sample Style:**
> "To say that there has been a lot of activity in the AI space for developers lately would be an understatement. As we transition from 'Ask' mode in our AI-based dev tooling to 'Agent' mode, it's easy to see agents as something magical."

### Key Evolution Drivers
- **Role Changes**: Microsoft → Google → Consulting led to broader perspective
- **Technology Shifts**: Windows → Web → Mobile → AI required continuous learning
- **Community Engagement**: Increased conference speaking and community interaction
- **Age and Experience**: Growing comfort with admitting uncertainty and sharing lessons learned

## Practical Style Guidelines

### Do's
✅ **Start with the reader's problem or need**
✅ **Use "I", "you", "we" freely for conversational tone**
✅ **Include complete, tested code examples**
✅ **Admit when you don't know something**
✅ **Use humor to make technical content more engaging**
✅ **Provide context before diving into technical details**
✅ **Link to sources and related reading**
✅ **End with a call to action or invitation for feedback**
✅ **Use personal anecdotes to illustrate points**
✅ **Be honest about limitations and trade-offs**

### Don'ts
❌ **Don't use jargon without explanation**
❌ **Don't make claims you can't verify**
❌ **Don't write in pure third person or academic voice**
❌ **Don't skip error handling in production examples**
❌ **Don't forget to test code before publishing**
❌ **Don't use humor at readers' expense**
❌ **Don't present preliminary work as finished solutions**
❌ **Don't ignore edge cases and gotchas**
❌ **Don't write without a clear problem-solution structure**
❌ **Don't forget to include necessary context for beginners**

### Content Checklist
Before publishing, ensure every post has:

**Structure:**
- [ ] Clear problem statement or motivation
- [ ] Logical flow from simple to complex
- [ ] Working code examples with full context
- [ ] Discussion of limitations or trade-offs
- [ ] Call to action or invitation for engagement

**Storytelling:**
- [ ] Opens from shared knowledge before introducing new concepts
- [ ] Includes at least one personal anecdote or real-world example
- [ ] Mentions unsuccessful attempts or dead ends when they illuminate the final solution
- [ ] Builds to a clear conclusion or next step, not just a list of facts

**Voice:**
- [ ] Conversational tone throughout
- [ ] Direct address to reader
- [ ] Appropriate humor or personality
- [ ] Personal insights or experience
- [ ] Honest assessment of uncertainty

**Technical Quality:**
- [ ] All code tested and working
- [ ] Necessary imports and setup included
- [ ] Error handling in production examples
- [ ] Links to documentation and sources
- [ ] Clear variable and function names

## Sample Templates

### Blog Post Opening Template
```markdown
# [Descriptive Title: What Problem This Solves]

<img src="_images/post-folder/hero-image.webp" class="main-blog-image" style="width: 250px" />

[Hook: Current situation, problem, or interesting observation]

[Context: Why this matters now, what changed]

[Personal connection: Why I care about this, my experience]

## [Section 1: The Problem/Background]

[Explanation accessible to general audience, then technical details]
```

### Technical Tutorial Template
```markdown
## [What We're Building]

Here's what we'll create:

```[language]
// Complete, working example of the end result
```

## Prerequisites

Before we start, you'll need:
- [Specific requirement with link]
- [Another requirement with version numbers]

## Step 1: [Descriptive Step Name]

[Why this step matters]

```[language]
// Code with comments explaining the why
```

[What just happened, any gotchas]
```

### Code Example Template
```dart
// Always start with imports
import 'package:necessary_package/package.dart';

// Use descriptive variable names
final meaningfulName = SomeClass(
  // Configure with realistic values
  apiKey: Platform.environment['API_KEY'],
  timeout: Duration(seconds: 30),
);

// Show error handling
if (meaningfulName == null) {
  throw StateError('Configuration missing: API_KEY environment variable');
}

// Demonstrate the main functionality
final result = await meaningfulName.performMainTask();
print('Result: $result');

// Include cleanup or next steps if applicable
```

## Conclusion

Chris Sells' writing style has evolved from formal technical documentation to a warm, conversational, and deeply knowledgeable voice that treats readers as colleagues and friends. The key is balancing technical depth with accessibility, honesty with enthusiasm, and personal voice with professional expertise.

The style works because it respects readers' intelligence while acknowledging that everyone is still learning. It builds trust through transparency, engagement through personality, and understanding through clear explanations and practical examples.

---

*This style guide is based on analysis of posts from sellsbrothers.com spanning 1995-2025. The style continues to evolve as technology and the developer community change.*
