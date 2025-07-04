Date: 11/11/2017

# Building a modern, mobile-first app

<img src="_images/IBM-Apple-apps-Retention-screenshot.jpg" class="main-blog-image" style="width: 250px">



A while ago, I started building [a brand new client app](http://sellsbrothers.com/backgammon-and-using-your-own-products). I wanted to use all of the modern techniques to make it a great app and to make sure it had maximum reach. For me, that meant a [Progressive Web App](https://developers.google.com/web/progressive-web-apps/) that would work from desktop and mobile browsers. And so that meant JavaScript. That was OK; I'd been through [the 5 stages of grief](https://grief.com/the-five-stages-of-grief/) for JavaScript [years ago](http://sellsbrothers.com/win8jsbook) but the real question is, which client-side framework do I choose? To answer that question, I spent a great deal of time with a LOT of them:

- [**Angular**](https://angular.io/): I was a huge AngularJS (v1) fan, but when the template for an Angular 2 project included as many files as for a J2EE project, I was out. Too much, too soon.
- [**Polymer**](https://www.polymer-project.org/): great framework with great UI components and tools, but the bet on standardizing [HTML Imports](https://caniuse.com/#feat=imports) as the core reuse mechanism didn't pay off. Instead, Polymer 3 seems to be heading down a "put your HTML into a string" road, which I don't like. Plus, the code I wrote in Polymer felt wrong somehow, particularly when it came to state management. This would become a theme.
- [**React/Redux**](React/Redux): I love writing React code. The rendering model really speaks to me. However, as soon as I wanted to share data between components, things broke down quickly. As much as I love React, I hate [Redux](https://redux.js.org/docs/basics/UsageWithReact.html). The model is nice but the code is insane. And the fact that there are dozens of other ways to manage state in React, fragmenting the community, is also insane. No thanks.
- **Lots o' little libs**: I spent some time going back to basics with HTML and [Bootstrap](http://getbootstrap.com/) and [jQuery](https://jquery.com/). And while I enjoyed picking up jQuery again and Bootstrap certainly met my UI needs, the lack of a virtual DOM or a standardized component model ([Custom Elements](https://caniuse.com/#feat=custom-elementsv1) still hasn't been adopted widely enough and there's a lot of boilerplate code) put me on a path of reinventing the solutions all of the other frameworks have already solved. Plus, still no great way to solve the state management problem.
- **[Stencil](https://stenciljs.com/)**: I really like the Stencil approach of pushing the common idioms (virtual-DOM-based rendering, JSX, class-based components) along with compile-time tooling to create HTML custom elements, but there aren't any UI frameworks for it yet. I didn't want my limited free time to start by wrapping Bootstrap in Stencil. According to [a recent podcast](https://www.acast.com/thewebplatformpodcast/138-stencil), the next version of Ionic will come with a set of Stencil UI components -- perhaps I'll revisit it then.
- **[VueJS](https://vuejs.org/)**: This is something I was looking into recently and it's very promising. It's simple, so it can be adopted easily, but full-featured so it can be used for real-world stuff. There's a range of usage from "include this JS file and write this code" to "run this CLI and get a Webpack-based build with hot-loading". There are plenty of UI frameworks, including for [Bootstrap](https://bootstrap-vue.js.org/). [Routing](https://vuejs.org/v2/guide/routing.html) turned out to be way simpler than in React and [state management](https://vuex.vuejs.org/en/) looks like it will be sane. I wish it were class-based, but Vue is still the #1 contender.

# Where are we?

Maybe it's just how I was brought up, but I miss the single-vendor, single-lib, single-tool, single-way model from my Microsoft days. It wasn't cross-platform or OSS, but WinForms was a great end-to-end solution for building Windows apps. Of course, I say all of this while still wanting a responsive app built using HTML and deployed via the browser that works everywhere for everyone. It's a heterogeneous world and that's a very good thing, but it does have it's downsides, too.

Tell me what you think -- did I mischaracterize your favorite JS framework or miss it altogether? I'd love to hear that there's an end-to-end toolchain that'll make me forget WinForms.

