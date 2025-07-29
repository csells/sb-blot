---
tags: ai
---

# A Young Software Engineer's Guide to AI

<img src="_images/young-swe-guide-to-ai/youtube-thumbnail.png" class="main-blog-image" style="width: 250px" />

I was on [an AI-focused podcast](https://youtu.be/AgSHvUgxio0?si=r7lxlL8uxhDGhKi5) last week talking about how a new software engineer should work differently in this new era of AI. It reminded me of a conversation I had recently with a college grad in their first professional software engineering career. They were asking for advice from a wizened old coder (I was even wearing the suspenders). With the latest in AI coding tools, they were productive for sure, but **they didn't understand *all* of the code that the AI agent was producing**. They were afraid that without being forced to write the code they old-fashioned way for *cough* 40 years *cough*, that they weren't ever really going to understand what they were doing.

I don't think that they are alone.

With that in mind, I've composed a letter for new SWEs just getting started with their careers.

## Dear New Software Engineer

Congratulations on landing your first job! I know it's exciting and terrifying at the same time. You're probably sitting in an open-plan office (sorry about that), watching veterans write code with instincts honed through years of hands-on experience. Watching them work, you're probably wondering how you'll  develop that same intuition when the latest corporate mandates encourage you to use AI coding agents. Especially  when those agents generate code you don't understand.

> **That's OK. You're not expected to understand all of the code you see at first, no matter who (or what) wrote it.**

But that doesn't mean you get to shrug your shoulders and press the commit button.

> **You're an engineer: it's your job to stand behind every line of code that you submit.**

If you don't understand what the AI is suggesting, then it's your job to learn, even if you no longer have to write that code yourself. The good news is you're in a better position to develop coding instincts than any generation of software engineers before you.

## How We Old-Timers Learned

Back in my day, we walked uphill both ways to compile our code. And we were happy!

It was a different time and we used different techniques. We spent countless hours reading other people's code - in books like Steve McConnell's *Code Complete*, in discussion groups, on mailing lists. We debugged other people's mistakes. We made our own mistakes. We'd argue about design patterns in Friday lunch-and-learns and spend weekends diving into open source projects just to understand how they worked.

The process was slow, sometimes tedious, but it built something invaluable: coding instincts. The ability to look at a piece of code and know "that's just wrong" even before you can articulate why. Having an opinion about what's okay and what's not OK in your code. Everyone develops their own instincts, but once you have them, you can spot problems that would take someone else hours to debug.

## Learn the Same Way, But Faster

Those same learning techniques still work today. In fact, they work better than ever because you have an all-day, every-day collaboration partner who never gets tired of your questions (sorry, Mike, I know you had other work to do...).

You don't have to wait for Friday's lunch-and-learn to discuss the code you read about in Jon Bentley's *Programming Pearls*. You can ask your AI to explain itself to you right now. When you see code you don't understand, ask questions:

- Why did you choose that approach?
- What other options were available?
- Is that the right solution for this specific problem?
- What are the trade-offs of this approach?
- Can you show me an alternative way to solve this?
- What happens if [edge case] occurs?
- Can you walk through this algorithm step by step?

Here's a conversation I had recently with my AI-coding-agent-of-the-week:

> **Me:** We (well, you really) just implemented simple hierarchical logging using a naming scheme, e.g. Logger('dartantic.chat.models'). I understand that the Dart logging package has a "hierarchical logging
>  feature built in. How does that differ from ours and should we be using that instead?
>
> **AI:** I'll take a look...
>
> *...looks it up...*
>
> **AI:** "Here's some sample code for using the hierarchical logging feature in the logging package:
>
> *...ugly, complicated hierarchical detail control code...*
>
> What you've done in this library is simpler and fits the needs of the library better. Let's keep using what you've already done."
>
> **Me:** Thanks. I agree.

I learned about a feature I didn't know existed, saw how it worked and then decided not to use it. That's exactly the kind of exploration we'd do in the olden days of yore but now it happens in minutes or hours instead of days or weeks.

## Go Further

But you don't have to stop at learning the way we old-timers; you can go beyond:

- Explore architectural patterns we never had time to try
- Experiment with different approaches in minutes instead of days  
- Get instant feedback on code quality and best practices
- Build prototypes to test ideas that would have taken weeks to implement
- Write the spec, generate the code, throw away the code, update the spec, generate the code again, etc.

> You're not just getting help from an AI coder. You're getting help from an AI coder trained on the collective knowledge of every software engineer who ever lived.

The patterns that took years to discover, the insights that made industry legends famous, the hard-won wisdom from decades of production systems - it's all there. You just have to ask.

## Coding Instincts - An Example

As you do this more, something interesting happens. You start developing opinions about the code you're seeing. When something *feels* wrong, you know it, even if you can't immediately explain why.

That's when conversations like this start happening:

> **AI:** I see the problem. It's a bug in the underlying library. I'll write a workaround.
>
> *...Me hitting the stop button...*
>
> **Me:** I'm sorry. I don't believe you. Are you saying that we've just discovered a new bug in the underlying library that happens to coincide with the new code we just wrote? That's as unlikely as getting hit by lightning while winning the lottery. I'd like you to write some diagnostic code to validate that claim before committing to a workaround, please.
>
> *...writing and running some diagnostic code...*
>
> **AI:** You're right! The problem is in our code...

That's what coding instincts look like. And it's that gut feeling that you're developing every time you ask the AI to explain itself, every time you ask for alternatives, every time you push back on its suggestions.

Don't just accept what the AI gives you. Ask it to write tests for the code it created. Ask it to add diagnostic output to validate its assumptions. Ask it to critique its own solution.

Every time you do this, you're doing the same thing we did when we spent hours in design review meetings or practiced test-driven development. You're just doing it faster, with more patience from your "mentor," and with access to more knowledge than any of us ever had.

## Keep Asking Why

I know you feel pressure to be immediately productive, to understand everything right away. Don't get caught up in that. Every software engineer is constantly learning. Even after 40 years of doing things the hard way, I still learn things from AI every day.

If you stay curious and engaged, your coding instincts will develop just fine. In fact, they'll probably develop faster and be more robust than mine ever were.

Yours sincerely,
Chris Sells

P.S. Keep a close eye on AI coders that wrap errors in exception handling in the name of "defensive coding" but really just to get the tests passing. They're sneaky that way...