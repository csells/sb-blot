Date: 11/12/2017

# WebAssembly *explodes* client-side programming
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/WebAssembly_Logo.png/440px-WebAssembly_Logo.png" class="main-blog-image" style="width: 250px">

Back in the olden days, you could use whatever programming language you wanted to write your client-side code: ASM, C, C++, BASIC, Java, C#, etc. Hell, every language had a client-side UI framework attached (often [more](http://sellsbrothers.com/wfbook) [than](http://sellsbrothers.com/wpfbook) [one](https://docs.microsoft.com/en-us/windows/uwp/get-started/universal-application-platform-guide)). Then the web hit and we used those same languages to generate HTML on the server-side. It was a polyglot world for client-side development right up until mobile made itself the #1 targeted format in the world (cue [record scratching noise](https://www.youtube.com/watch?v=sqgW-2orQQg)).

In a mobile/cloud world, the server is alive with programming languages, including Java, C#, Swift, JavaScript, Erlang, Clojure, Go and 100 others. But on the client-side, we're relegated to just a handful:

* Mobile Web: JavaScript (and its variants)
* Mobile Native: C/C++, Java, Objective-C/Swift, C#

That's pretty much it. As a C# guy personally interested in building a web app (specifically a [Progressive Web App](https://developers.google.com/web/progressive-web-apps/) that works great on both desktop and mobile devices), I was pretty frustrated. I could write any language I wanted so long as it was JavaScript.

If you want, you could point out the large number of languages that "transpile" to JavaScript, including some C# variants, but every one of those that I've tried has been a very leaky abstraction -- you have to know that they are just JavaScript translators and write your code accordingly. It's a very different experience than the multi-language support on the Java VM or .NET, where the VM provides a common environment for every language without any one language being "special" (although you could argue that Java shines through into languages like Clojure and C# into F#, but not nearly to the same degree). Really, in the browser, JavaScript is the only first-class language, along with HTML being the only UI framework.

So, with PWAs on my mind, HTML in my heart and JavaScript at my fingers, I started [looking into JavaScript frameworks](http://sellsbrothers.com/building-a-modern-mobile-first-app). Unfortunately, they all of them left me wanting something much more like those old Microsoft UI frameworks, but for the web.

And then came Steve.

# The mother of all web demos

In June of 2017, Steve Sanderson gave a talk entitled [Web Apps can’t really do *that*, can they?](https://www.youtube.com/watch?v=MiLAE6HMr10) in which he demonstrated the following:

* [DotNetAnywhere](https://github.com/chrisdunelm/DotNetAnywhere) runtime compiled to [WebAssembly](http://webassembly.org/)
* [.NET Hello, World run via WebAssembly](http://sellsbrothers.com/public/post-images/2017/steve1.png) in DotNetAnywhere
* [Blazor](https://github.com/SteveSanderson/Blazor), an experimental client-side framework for building [Single Page Apps](https://en.wikipedia.org/wiki/Single-page_application) in .NET
* ASP.NET [Razor template expansion](http://sellsbrothers.com/public/post-images/2017/steve2.png) (including embedded C#) on the client-side
* Hot-loading (a code change recompiles the app via in-memory, server-side Roslyn compiler and reloads the app on the client-side)
* Dynamically loaded polyfill for browsers that don't support WebAssembly, i.e. IE
* [Debugging on the client-side in C#](https://twitter.com/davidfowl/status/891125650286755840) (developed in a hackathon after Steve's talk)
* [Shared C# classes between the client-side and server-side (aka "reified classes")](https://github.com/SteveSanderson/Blazor/blob/master/samples/ClientServerApp/ClientServerApp.Shared/WeatherForecast.cs), serialized via JSON and passed around via REST (not mentioned in Steve's talk, but available in his sample code)
* Classic Todo List app implemented from scratch in 5 minutes flat, with a size of only 326K:

<img src="http://sellsbrothers.com/public/post-images/2017/steve3.png">

This talk blew my mind. This was the browser-based full stack for .NET that I hadn't even realized that I'd wanted. Steve's demo made me realize that not only could Microsoft create an amazing end-to-end experience for .NET developers that wanted their code to fit into the modern web, but they could do it in a way that worked across all form factors and all platforms.

This demo wasn't the end. [Steve recently posted](http://blog.stevensanderson.com/2017/11/05/blazor-on-mono/) an update talking about all of the contributors he's had since his demo, but also the move to the more full-featured Mono .NET implementation provided by Xamarin.

# What's coming

Anyone experienced with Microsoft sees a clear path sketched out by the TOC to any Windows-based UI framework book:

* **Easy onboarding** with templates for Visual Studio [Code] to help developers quickly build .NET-based client-side and full-stack apps
* **Great tooling:** Intellisense, Goto Definition, debugging, etc. will all be baked in
* **Components:** out of the box components will be great and cover the common cases
* **Component Ecosystem:** non-UI libraries and advanced UI use cases will be served by a 3rd party community (can you say "Json.NET and Telerik"?)
* **Data Binding/State Management:** Microsoft invented data binding for Visual Basic and Steve already showed this off during his demo
* **HTML Templates:** ASP invented the inside-out model we think of as modern HTML templates and Steve has already shown Razor redone for .NET and the web
* **Reified classes:** Silverlight did this thing were you could send .NET objects over the wire and use the same class implementation on both sides. Steve's already shown how this can be done over the web using JSON and REST, which means that any language can play on either side. Do this with [GraphQL](http://graphql.org/) and you'll get querying only what you need and data change notifications via subscriptions.
* et cetera

And as huge as this will be for the .NET community (can you say "Silverlight without the mess"?), that's not all.

# WebAssembly is the key

Right now, Steve's proof of concept .NET Runtime is a 4MB download for any .NET app. That's clearly not going to work. As Steve mentions, there are lots of optimizations to do that'll bring down the size, but the real optimization win is going to be when the next version of WebAssembly comes out, including two big features: plugging into the browser's VM and access to the DOM. The WebAssembly working group are already discussing both of these (e.g. here're [the notes from a recent meeting discussing garbage collection](https://github.com/WebAssembly/meetings/blob/1e6144bdfa932eb137d93cfc3507bb7b08a5cf6a/2017/CG-07.md#gc--managed-data)).

With access to the browser's VM, a .NET app won't need to ship it's own runtime for every app -- it'll just plug .NET objects into the one provided by the browser. Now the .NET runtime for the browser can be trimmed to a bridge to what the browser provides as well as the .NET Framework base libraries. Combine that with "tree shaking" (removing the IL your code never uses), code splitting and on-demand loading and you've got a .NET WebAssembly app that even anyone on just their phone can love.

But it's really the 2nd thing that the WebAssembly working group is enabling -- access to the DOM -- that enables a Cambrian explosion of client-side programming languages. WebAssembly already provides a language-independent calling layer enabling various languages to call not only between itself and JavaScript but also between every other language that compiles down to WebAssembly. Combine that with a standardized UI layer -- the HTML DOM -- and **WebAssembly enables a universal client-side runtime for any language**. What this means is that I no longer have to have the Angular, React and Vue bindings to Bootstrap: if someone provides the UI components for Bootstrap in any language that compiles down to WebAssembly, I can use it from any other language.

# Just one more thing

And as amazing as WebAssembly is about to get for the client-side, I doubt this thing will stop there. NodeJS was invented to bring JavaScript and it's underlying runtime to the server. Imagine doing the same thing for WebAssembly but for any language, server-side or client-side. Done right, WebAssembly has the potential to be what Java and .NET have both tried and failed to be -- the universal runtime for all languages across all platforms.

But even if it doesn't get that far, Steve has shown us that WebAssembly enables an amazing client-side experience. I can't wait to get my hands on it.