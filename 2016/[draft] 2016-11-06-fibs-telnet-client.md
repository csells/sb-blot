# FIBS: Initial .NET Core Library
<img src="http://sellsbrothers.com/public/post-images/2016/fibs-rules.png" class="main-blog-image" style="width: 250px" />

Recall from [my last post](/backgammon-and-using-your-own-products) that I'm building up a web client for the First Internet Backgammon Server (FIBS). I'm a fan of iterative development, so the first thing I want to do is get some bits flowing between the FIBS telnet server (at fibs.com:4321) and my .NET Core code. My plan is to build up a little FIBS library that knows how to manage the connection, login, send commands, process messages, logout, etc. Eventually I'll build up a proxy that can speak the WebSocket protocol and sit between my JavaScript code and the FIBS server, using my library to handle the connection, but in the meantime, I'll just use a Console app to host the library.

Note that I casually threw in that I was going to use .NET Core. This is a new project and in spite of the fact that the Windows version of .NET has been GA since 2002, I'm still going to throw my hat into the .NET Core world. It's clearly where Microsoft is putting it's major muscle and it's cross-platform, so even though I can build and test it using Visual Studio running on Windows, I can eventually deploy it in a Linux Container running on [Google App Engine Flexible Environment](https://cloud.google.com/appengine/docs/flexible/) aka App Engine Flex.

While I've made lots of progress on all of [the Fibs.Net code](https://github.com/csells/fibs.net) before writing this post, I'd like to rewind history a little to talk about the process of getting there. [The code for this post](https://github.com/csells/Fibs.Net/tree/4f5380421565f502a22caadddbf3f69218110535) is back in history a little, but we'll catch up soon enough.

## Parsing FIBS Text
Before we dig into the implementation details, let's talk about what we need to do here. Fundamentally, telnet is a connection-oriented unstructured text protocol. 

## .NET Core + Telnet
As it happens, Telnet is a scary protocol without any built-in support from .NET Core. The thing that makes it scary is all kinds of control characters sent to the client to arrange text on the screen of an assuming VT100 terminal. What I'm looking for is something to handle that for me, and I did, in [Tom Janssens's minimalistic Telnet library](http://www.codeproject.com/Articles/19071/Quick-tool-A-minimalistic-Telnet-library) from 2007.

Tom's telnet library is great because it does exactly what I wanted -- it handles the telnet option requests by saying "no thank you" back to the server and only hands out the actual string data.