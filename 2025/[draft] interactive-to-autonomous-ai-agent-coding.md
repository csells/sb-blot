# Moving from Interative to Autonomous AI Agents for Coding

TODO: blog post here

## Outline

- been working on rebuilding [dartantic_ai](https://github.com/csells/dartantic_ai) from scratch using the multi-provider compat layer that David built in his [most excellent langchain_dart implementation](https://github.com/davidmigloz/langchain_dart) (don't ask me why I've got a functional replacement for dartantic_ai all as an unpublished PR inside a fork of David's library -- I started experimenting there and it got out of control. I promise to fix it soon!)
- have had a LOT of luck driving an AI agent interactively (Cursor, Sourcegraph Amp, Claude Code, Cline):
	- essentially rebuilt dartantic_ai in two weeks with more providers, more features and more tests
	- used a simple workflow
		- start with "planning mode" (either manually or via a product feature) to iterate on whatever the next feature was, e.g. each model provider can provide a list of models that they support, e.g. "googe:gemini-2.5-flash", "openai:gpt-4o", etc.
			- describe what I want in as much detail as I can
			- before submitting the prompt, add "ask any questions you may have before you get started." (I have a hotkey for this)
			- iterate until you have a plan you like
			- dump the plan to a markdown spec file
		- execute on the plan until the errors are fixed
		- write the tests and run them (including the old ones in case you broke something) till they pass -- spending a lot more time reading and validating the tests then the code itself
		- update the spec based on real-life implementaiton details
			- useful for swapping context back in for the AI for future work
		- commit and repeat
- at this point, the project has grown to a non-trivial size: 20K LOC, 4.5K lines of Specs, 1.1K tests, multiple architectural layers
	- this is small for production code of course, but already I'm spending time watching the stream of consciousness of the AI, i.e. what code it's producing and why, because I find that if I don't, it goes off into the weeds with implementation choices that clearly break existing functionality or design choices that lead to spaghetti code
* issues I'm running into:
	- Agent deciding that the tests take too long to run the tests, so it starts removing some of the things we're testing (without keeping in mind the testing spec that says not to do that)
	- Agents focused on fixing a specific test failure without keeping in mind the specs for the entire system where their fix is clearly breaking something else; the agent just runs the individual test and declares victory
	- Agents fixing issues with try-catch blocks and logging instead of tracking down the source of the issues; same with using default values for null results where no null results should be happening (sometimes a null is possible in the general case from an API but indicates an error in your specific app)
	  - I have not had luck fixing this problem using AGENT-NAME.md or memory or rules
	  - I've had some luck reducing this by:
	  	- having the AI plaster a giant comment block at the top of every test file that says not to do this so it's force to read it every time it's working with tests: [langchain\_dart/packages/langchain\_compat/test/agent\_orchestration\_test.dart at compat-only · csells/langchain\_dart · GitHub](https://github.com/csells/langchain_dart/blob/compat-only/packages/langchain_compat/test/agent_orchestration_test.dart#L1)
	  	- building a custom linter that points out when the AI has created a try-catch block that does not throw or rethrow: [GitHub - csells/exception\_hiding\_lint: A custom Dart lint package that detects exception hiding patterns in your code.](https://github.com/csells/exception_hiding_lint)
	- Agents declaring that an external SaaS doesn't support a specific feature needed to implement something in the app, so just walling them off from the app without doing the research to see if that's true or not (without remembering our principles of doing that research first before declaring failure)
	- Agents producing complex, fragile code that breaks as soon as new features are adding by simple banging in patches wherever it seems useful, again without taking into account the breaks they're probably inducing (they don't see able to keep the design of the system in their head)
	  - I've had some luck solving this problem with interactive architectural redirects (defeats the autonomous AI agent goal)
	  - I've done one major re-architecture with the agent, which cleaned some things up
	- Agents ignoring project-specific priciples, e.g. I'm working with LLM providers that have uneven support for Json Schema, so trying to take them into not just cramming things in is a constant challenge
	- Agents with long task list missing key pieces of every step, e.g. make sure to run the tests and the samples to make sure you haven't broken anything
	- Agents with long task list just stopping after running a few of them
- not only are these issues I find that I'm constantly combatting against, but I want to take the next step and build single-shot prompts that get me to PRs that are 90% there in terms of functionality, a robust implementation and an architecture that can be flexible in the face of future needs. I expect to have feedback but to get to a place where an initial implementation is largely there, i.e. moving from interactive use of AI agents to autonomous use
	- this is what allows me to scale from pair programmer with a total team of two (one keeping the architecture and goals and principles in mind and one typing really, really fast) to an eng manager with a team as large as I can handle (I know people that do this with 10 (!) agents working simultaneously!)
	
- Next steps
	- experiment with more detailed specs produced in conjunction with the AI: [AGENT MODEL MAPPER REFACTORING.md](https://github.com/csells/langchain_dart/blob/compat-only/packages/langchain_compat/specs/AGENT_MODEL_MAPPER_REFACTORING.md)
	- lean into tools like [Terragon](https://www.terragonlabs.com) that not only provide for agents building things and producing PRs for you in the cloud but also the ability to bring that change right to your claude code instance to put up the code and the conversation where they left off!
	- Jam with folks like [Geoffrey Huntly](https://ghuntley.com/six-month-recap/) and [GosuCoder](https://www.youtube.com/@GosuCoder), who are on the same path that I am.
	  - https://chatgpt.com/c/68749b50-3428-800e-986e-118b093ce302
	
	- this dude: https://youtu.be/Q_u_E0br820?si=joii5_nsvDmoLa2j
	- and this dude: https://www.youtube.com/watch?v=3PdorDOn6xI
	- and this other dude: https://www.reddit.com/r/ClaudeAI/comments/1lyuccp/im_using_gemini_as_a_project_manager_for_claude/
	- and this dude for sure! https://buildermethods.com/agent-os
	- and this? https://github.com/Pimzino/claude-code-spec-workflow
	- More Tmux Orchestrator: https://youtu.be/UgHbHqg_Wmo?si=fLvfTn-f9-u-cgq1
	- Infinite Loop?
	  - https://youtu.be/9ipM_vDwflI?si=gHRFOwh7gfIgv_WE
	- PM pushing the Dev via tmux!
	  - https://www.youtube.com/watch?v=rA7DbDCJhl8
	  - https://github.com/Jedward23/Tmux-Orchestrator
	
	- take input from you guys! are you having success with autonomous AI agents? how are you doing it? don't keep me in suspense!