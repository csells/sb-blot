# The 5 Stages of AI Grief

> *"The future is already here – it's just not very evenly distributed."* –W. Gibson

As a consultant and speaker, I talk to a lot of software engineers. As AI coding tools have gotten better (and they have gotten *much* better), I've watched engineers move through a set of stages as they evolve in their understanding and embracing of these new tools. Change is not easy. These stages can be painful and every software engineer is progressing at their own pace.

Call them the 5 stages of AI grief.

## 1. Denial: "AI is a fad. It'll go away soon."

TODO: pic

I still run into a large number of engineers who are convinced that AI coding is just hype that'll blow over like so many other tech fads. They're the ones who haven't touched ChatGPT or CoPilot or Claude. They've been writing code the same way their whole lives and why should they change now?

These are often the same people who said the web was a fad in 1995. History has a way of repeating itself.

## 2. Anger: "AI doesn't work. I tried it two years ago and it sucked."

TODO: pic

This is where things get interesting. These engineers *did* try AI coding tools, but they tried them when they were genuinely pretty bad. GPT-3.5 in early 2023 was... let's call it "inconsistent." So they tried it, it failed to solve their problem, and they walked away convinced that AI coding was overhyped nonsense.

Or, these are the engineers who have strict company policies about which tools and which LLMs that can and can't use, often in-house, self-made stuff that leaves something to be desired. To be fair, these home-grown tools often do suck.

The problem is that these engineers haven't (or aren't allowed to) try the current crop of tools. They don't realize that the AI that couldn't write a proper for-loop eighteen months ago can now architect entire applications, if used correctly. But until they get over that hump, they're not going to see the value in them.

"I tried it and it hallucinated." Well, yeah. It did. And it still does. But not mostly only if you're holding it wrong.

## 3. Depression: Vibe coding: "Build me a healthcare app"

TODO: pic

This is the stage where engineers realize AI actually works, but they're holding it wrong. They're trying to skip from zero to sixty with prompts like "Build me a healthcare app" or "Make this code better."

Vibe coding works great when you're building something small and self-contained. But then you ship that healthcare app and get hacked with a SQL injection attack because you're not an engineer at all and you don't know what SQL injection is. That's when the depression sets in.

The tool isn't the problem. The problem is treating AI like a magic genie instead of a powerful tool that needs to be wielded thoughtfully by someone who understands the domain.

## 4. Bargaining: Describe coding: "Let's design a new feature"

TODO: pic

I don't do "vibe coding." OK. Well. I have "experimented" with it. But I was younger. And I needed the money. And it was tastefully done!

Now, I've grown a bit and realize that there are better ways, I've adopted a technique I call "describe coding" (patent pending. all rights reserved. I do own the domain name : ). Instead of leaping right into "build me a thing that does a thing", I start with "let's think about this for a minute." I like AI coding tools that have a planning mode that stops them from jumping into coding, but even without that mode I use prompts like "I'd like to design a ... It should work like ... It should have these constraints... ask any questions you may have before you start coding." Actually, that last sentence I use so often, I've stored it in a snippet behind a hotkey on my mac.

I used to use PRDs to describe things, but now I collaborate on the AI until we come up with a design I like. Then the implementation. Then fixing the warnings as well as the errors. Then I tend to dump each substantial thing to a design doc for future use. It's helpful to get myself and the AI on the same page when it's time for future work. Then generate tests and run them till they pass, being careful to really check that the tests model the behavior I want. Then update the docs (README, etc) and the CHANGELOG and then a commit or PR, depending.

Notice what I'm doing: design => implementation => design doc (with implementation knowledge) => testing => ...

This is the standard practice that we've been doing for decades. It starts with a description of what I want ("design coding") and goes on from there. You could call the use of my traditional software engineering techniques "bargaining" but I like to think of it as applying the techniques that really work, but in new ways. 

And in this new way, that's where the magic happens. This technique along lets me code in minutes or hours what used to take days or weeks. The AI becomes a super-fast pair programming partner instead of a glorified autocomplete.

TODO: so much fucking fun!

And there's still one more level to go!

## 5. Acceptance: Orchestrating agent swarms

TODO: pic

The engineers who've made it to acceptance aren't just using AI to write code. They're orchestrating whole sets of them. Personally, I like to keep about three projects going at once, switching between them, using different instances of my favorite AI coding agent of the week (things are changing *really* fast). And even though I keep it to three projects, I still find myself with the "oops wrong chat" problem, so three may be my limit.

On the other hand, some people are combining AI agents at the command line with tmux and a little known feature of git called "work trees." The work trees feature creates a folder of duplicate code for each branch so that each agent gets it's own space to work in, e.g. one adding logging, one adding error handling, one checking security, one adding the login features, etc.

Once you're swarming, you're not a software engineer anymore -- now you're an engineering manager with a lot of opinions about what everyone on your team should be doing and how they should be doing it. And with scale comes increased productivity.

Swarm mode is where your points *really* rack up.

## 6. Denial... Again: "Remote coding"

TODO: pic

But here's where it gets really interesting. Just when you think you've accepted AI coding, something new comes along that pushes you back to stage one. Remote coding - where you give AI agents your repo and ask them to do things, and they can create entire PRs without you ever seeing the code or the running app - is here. Cursor just launched a site for this (TODO:link), but also Google's Jules and TODO's something else.

And suddenly, engineers who were comfortable with AI pair programming are back to "this is just a fad" because the idea of AI writing code they can't see feels like giving up control entirely. I'm not ready for that.

The cycle begins again.

## The Questions We're All Asking

As I've watched people move through these stages, some questions keep coming up:

**Will using AI atrophy my knowledge?** Some skills will almost certainly atrophy - that's what happens to muscles that aren't used. But we lost our tails because we never used them either. If something dries up and falls off due to misuse, how important was it in the first place?

Here's how I think about it: I was never an assembly programmer, but I remember when C was new. All the assembly programmers were skeptical about it being able to produce assembly as well as they could, so they spent time poring over the generated assembly code, pointing out all the issues it had compared to hand-rolled assembly. At the time, nobody trusted the C compiler to write good assembly code. 

Now, nobody trusts humans to write good assembly code, because the compiler does it better and oh so much faster. 

My question is: how much longer will it be before you don't trust the code *unless* it was written, reviewed, and tested by an AI?

**Will AI take my job?** I honestly don't know. But I do know this: software engineers who use AI tooling are going to be more effective and efficient than those who don't. And when promotion time comes around, or when layoffs happen, those are good qualities to have.

**Will AI produce more, different jobs?** Here's how I think about it: every single technology we've ever invented has put someone out of work. When Grog the plump caveman lost his cuddling-to-keep-the-tribe-warm job when fire was invented, he thought he'd lost those extra berries at bedtime for good. 

On the other hand, I'd argue that fire has been the source of every job that was created since it was invented. Do we want to get rid of fire? It's pretty useful.

The problem is that every new technology seems like it's different - that this one *only* destroys jobs and doesn't create them - because the lost jobs happen first and we can't see into the future. The Luddites broke into fabric mills and destroyed steam-powered looms because they didn't want to lose their jobs. Of course, we ended up creating more jobs with the machines that looms ultimately led to.

Is AI going to be different? Is it only going to destroy jobs and not create them? I don't know. I can only say that if that were the case, it would be unique in human history.

## Where Are We?

All technology advances happen faster now. The spread of electricity took ~45 years to reach 25% of the US population. The internet took ~7 years. AI has been making changes in our society as large as those other two in just 2.5 years. What that means for us puny humans, I do not know.

But here's what I do know: the grief you're experiencing with the oncoming AI is real grief. We are losing things with AI. We're going to lose knowledge and skills. Artists and other content creators seems to have lost their IP. The new people entering every field that uses AI are going to do things differently; the old ways will be lost.

The question isn't whether we're going to lose things with AI - we already have. The question is: is what we gain going to be worth it?

Will it be a higher standard of living for our children? Will AI help us solve real-world problems that we've been unable to solve ourselves? Once we get over the grief, will we be able to experience joy in the new world that we've created for ourselves?

I hope so.

The engineers who are curious, who are experimenting, who are moving through these stages thoughtfully - I believe that they'll be fine. The ones who get permanently stuck in anger or denial? I think they'll have a harder time.

The future is already here. What are you going to do about it?