Date: 11/5/2016

# Backgammon and Using Your Own Products
<img src="/public/post-images/fibs-logo.png" class="main-blog-image" style="width: 400px" />
Have you ever used a product of any kind -- an app, a device, a plunger -- and thought to yourself "Do these people even use their own product?" As a Product Manager at Google and a long-time software product guy, I think this all the time. Sure, [Donald Norman](https://www.amazon.com/Design-Everyday-Things-Donald-Norman/dp/1452654123), [Steve Krug](https://www.amazon.com/dp/0321344758/) and [countless others](https://www.amazon.com/s/field-keywords=product+design) have written well on the topic of product design, but that's not enough.

**You actually have to use your own products to solve the same problems your target customers are trying to solve.**

If you don't, you're much less likely to produce something that anyone will want to use.

## Method Acting and Product Design
Of course, using your own products comes with it's own set of problems and there's the very real possibility that you're just not your target customer -- in those cases, you just have to fake it. Consider this the "method acting" form of product design:

**Put yourself in a situation where you need to use your product the way your customers will use it.**

Got a map app? Use it to find something you really care about finding. Got a device for kids that cooks pizza at the press of a button? Ask your children to make dinner and eat the results. Got a solar-powered plunger? Plug your toilet in the middle of the night and see what happens.

In my case, the products I design are for use by software developers. That means that to test my own products, I have to set myself up in a situation where I'll need the product I'm building.

As an example, a couple of years ago I was writing [an online course about building cross-platform apps using Xamarin](http://my.safaribooksonline.com/video/programming/mobile/9780134052144). I could've showed off the various topics with throw-away samples, but then I would have no confidence that I'd covered the topics my developer customers cared about. Instead, I chose to start from scratch and build an app I actually wanted for use on Windows, iOS and Android phones and tablets. In that way, my course became the thing I wished I'd had to get up to speed on building apps with Xamarin.

**Caring about the results of using your product is key.**

Without caring about the results, you can't properly judge whether your product is useful or not. If you don't care whether you get a vegetarian pizza or a meat-lover's, you're not going to be able to notice important issues in your EZ-Bake Pizza Oven, let alone fix them.

In my case with the online course, if I hadn't have chosen to build something I wanted to use on my own phone and to share with friends and family using a variety of other phones, then I wouldn't have had the module on deployment for each of the three mobile OSes I was asking my customers to target. Without that module, my customers would've been stuck figuring that out for themselves, making my online course a lot less useful.

## First Internet Backgammon Server
A few days ago, I celebrated my two-year Googleversary; I'm the Lead Product Manager for [the Google Cloud Plaform](http://cloud.google.com) developer tools. In that time, I've built a few small apps to wrap my head around the products we're building for our developer customers, some of them are real-world (like tinysells.com for resolving the URLs in my books), but most are throw-away demos. Even the real-world apps are small enough that I haven't needed to really embrace a large surface of our platform. For this to qualify as a real product test, it needs to be cloud-based, it needs to be big enough to exercise a reasonable subset of our platform and most importantly, it needs to be something I care about.

**I care about Backgammon.**

As a kid growing up in the Midwest, my family was big into card games, board games and strategy games of all kinds. It started at a young age with Double Dirty Crazy 8s, Monopoly, Risk and Speed and eventually graduated to Gin, Cribbage, Poker and, of course, Backgammon. My family wasn't one of those "let the kids win to build their self esteem" families -- we played to win and then we'd gloat afterwards. To this day, I still play a lot of these same games on my phone (the real reason I use Android isn't because I work at Google, but because of [the world's best mobile Risk game](https://play.google.com/store/apps/details?id=com.game.drisk)).

I play [my favorite Android Backgammmon game](https://play.google.com/store/apps/details?id=uk.co.aifactory.backgammonfree) several times/day. I'd like to play with other humans, but unfortunately, it's not online multi-player and I moved away from my game-crazy extended family years ago. I would play Backgammon with my local immediate family, but they don't like to play with me (I can't imagine why).

Of course, there are plenty of existing ways to play multi-player Backgammon online, but none of them as the same UX as the one from AI Factory. Plus, the internet has a hidden gem for Backgammon that I would love to access via a modern UX: FIBS.

The [First Internet Backgammon Server](http://fibs.com) is a telnet server that has been running since 1992 to provide multi-player Backgammon via text over the internet. It was originally created by Andreas (marvin) Schneider and has been maintained by Patti Beadles since 1996. According to [FIBS history](http://www.fibs.com/guide.html#history), FIBS has "[a number of excellent graphical interfaces](http://www.fibs.com/connecting.html)". Well, excellent than may be, but I couldn't get any of them to work. All of the ones I could actually get to run seem to be largely GUIs around the same [telnet command line interface](http://www.fibs.com/CommandReference/index.html) that FIBS has always provided. Don't get me wrong -- FIBS commands are great for text mode ([the FIBS text board layout](http://www.fibs.com/fibs_interface.html#board_state) is especially fun), but it doesn't hold up to the expectations set by modern mobile Backgammon games, especially the one that the AI Factory has me hooked on.

As a user of FIBS, I want a responsive web UI on my desktop or mobile browser that meets modern UX standards; the only time I want to type anything is when I'm chatting with Backgammon friends and enemies.

As the developer of a web-based FIBS client, I have to worry about the following things:
- a web frontend so anyone can access it from anywhere
- a WebSocket proxy so that the web frontend can talk to it (JavaScript can't open a connection to a raw telnet server)
- a backend in the cloud so it can scale with demand
- a way to notify users when it's there turn (Pam hates [droppers](http://www.fibs.com/guide.html#community)!)
- a CI/CD pipeline to get updates out to users ASAP
- a way to track my app in production for perf, usage, crashes, etc.

As a product designer (remember where all of this started), I get to push on the following pieces of our cloud developer tooling:
- how do I learn about targeting the cloud with my app?
- how do I build my backend and my frontend?
- how do I validate an update to my app before deployment?
- how do I deploy an update?
- how do I monitor my app in production?
- how do I track errors back to causes?

## Where Are We?
Each successive layer from user needs to developer needs to product design validation is driven by the core: I'm building something I care about. To be clear, I picked a project that execises the end-to-end of my product set on purpose. Further, any one app I build will only be one of a nearly infinite number of possible passways through the features and tools we provide to developers on the Googles Cloud Platform.

Of course, using your own product can't be the only thing need you do to validate it. You also need input from customers, competitive analysis, usage metrics, ...

That said, **using our products like a real customer is going to give me a bunch of insights that I wouldn't have had otherwise**. That's a good thing.