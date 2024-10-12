# Minimal Reproduction Projects

TODO: explanation

TODO: example

Aug 9, 2022, 8:16 AM
Can you please help me with go router implementation?
I am opening the dialog by using this route.

If i am at route

/main/1

And trying to open dialog by using

/main/1/dialog

Than its redirect same page and than open the dialog

Can you please tell what wrong I'm doing in this?

Aug 9, 2022, 8:18 AM
Can you send me a minimal repro project so I can take a look?
Aug 9, 2022, 8:18 AM
Yeah, will do that
Aug 9, 2022, 8:18 AM
Please send the smallest possible project that shows the trouble you're having
Aug 9, 2022, 8:19 AM
Yeah sure will send you
Aug 9, 2022, 8:20 AM
https://t.co/BvyIxwyVa4
Here is the can you please check that
Link***
Aug 9, 2022, 8:21 AM
I'll take a look when I get a chance
Aug 9, 2022, 8:22 AM
Yeah, sure
Thanks
Aug 9, 2022, 8:22 AM
It should be just two files: the main.dart and the pubspec.yaml.
Aug 9, 2022, 8:23 AM
Okay will send you only that
Aug 9, 2022, 8:24 AM
"Please send the smallest possible project that shows the trouble you're having"
Aug 9, 2022, 8:24 AM
Yeah sure
Aug 9, 2022, 8:24 AM
how can a minimal repro be 1.2GB??
Aug 9, 2022, 9:33 AM
https://t.co/xLDTNB2R45
Aug 9, 2022, 9:34 AM
Will send a file which only have a logic part
Aug 9, 2022, 9:39 AM
the project must be runnable and have all extraneous code trimmed out of it
no app-specific logic
Aug 9, 2022, 9:40 AM
Yeah sure
Aug 9, 2022, 9:45 AM
https://t.co/FNLl0d8m1M
Can you please check this?
Here is the code which contain logic part only
In this I'm trying to open the dialog via link
But it's getting open twice
And can you please also help me how the URL is also changing if we open the dialog and close the dialog
Aug 9, 2022, 10:11 AM
happy to help. can you cut it down to the minimal repro w/o any app-specific code? thanks.
Aug 9, 2022, 11:10 AM
Yeah, sure thanks for help ☺️
Can you please tell me is it good if I'll share pubspec.yaml and main.dart only?
Aug 10, 2022, 8:53 AM
That's the ideal but I need to be able to run it.
Aug 10, 2022, 8:55 AM
Yeah, sure you would be able to run that
Will share that in a bit
Aug 10, 2022, 8:56 AM
https://t.co/95C53h2OWM
Can you please check this link
I have shared both that files
Aug 10, 2022, 9:07 AM
that is an excellent minimal repro
can you now tell me how to use the app to trigger the issue you're seeing? and what behavior you're expecting?
Aug 10, 2022, 9:32 AM
Yeah, sure
Aug 10, 2022, 9:32 AM
Actually I want to achieve something like that.

I want to open the dialog from main screen just adding dialog as path param.

Once dialog is open the path param should be part of url and once dailog is getting closed tha path is also should be set-up with the actual one.
Can we achieve something like that?
Actually I have tried couple of ways but nothing is working as i expected
I want to achieve by adding. | Or
But dialog is getting open twice
Aug 10, 2022, 9:36 AM
/:tab(main|dialog)
I have tried something like this but dialog is getting open twice
Aug 10, 2022, 9:39 AM
I'm sorry
can you tell me the instructions for what to click in the app?
what happens and what you want to happen?
Aug 10, 2022, 9:42 AM
Actually I want to open the dialog by using the URL. Over main screen
Is that possible?
URL should be main route or subroute
As we are changing the URL from browser we also have to change the widgets as well or vice versa
Aug 10, 2022, 9:45 AM
As we are changing the URL from browser we also have to change the widgets as well or vice versa

-> it will be great if you can tell me this how widgets and URL is change simultaneously as per action or by deep linking
Aug 10, 2022, 9:48 AM
so this is meant to be a Flutter web app?
Aug 10, 2022, 9:51 AM
Yeah, exactly
Aug 10, 2022, 9:52 AM
what will the user enter into the address bar?
Aug 10, 2022, 9:52 AM
URL will be like this

/main - should open the main screen
/main/dialog - should open the dialog over main screen
Aug 10, 2022, 9:53 AM
what happens in the sample when you surf to /main/dialog ?
and what do you expect to happen?
Aug 10, 2022, 9:57 AM
Actually if i am trying to do this it's throwing error for same link redirect
I want to open the dialog by link that show open the form within that dialog
Want to achieve that kind of functionality
Aug 10, 2022, 9:59 AM
you don't have a route defined for /main/dialog
Aug 10, 2022, 10:00 AM
Actually we have to put logic manually  for changing the widget as well as changeing the URL
Aug 10, 2022, 10:01 AM
it's defined for /main or /dialog
Aug 10, 2022, 10:01 AM
Yeah, i have tried couple of way but it's not working
Aug 10, 2022, 10:01 AM
here's how you define subroutes: https://t.co/oAfAtdNF8R
Aug 10, 2022, 10:02 AM
Can you please provide me any of way that is working like this
Yeah, i have checked that but for dialog the link is not getting change if we are popping out the dialog
Or I can not found any solution for dialog kind of functionality
All the examples are talking about screen
Can you please help me for this dialog one?
Aug 10, 2022, 10:03 AM
go_router is oriented around screens to support deep linking
you can show a form on it's own screen w/o issue
are you trying to show a popup dialog?
on the main screen?
Aug 10, 2022, 10:04 AM
Yeah
Correct
Dialog over main screen
Aug 10, 2022, 10:04 AM
I'd do something like this then /main?dialog=true
then when you're showing the main screen, you can show the popup dialog or not based on the query parameter
https://t.co/Hwao0zxcqr
Aug 10, 2022, 10:05 AM
Sounds great
Thanks 🙏🙏🙏
Great idea
Very helpful person you are
Thank you so much ❤️
Aug 10, 2022, 10:06 AM
happy to help
good luck!
Aug 10, 2022, 10:06 AM
Yeah it's a very great package and I'm really enjoying by using this
Thanks for this awesome package 👏👏👏👏
Aug 10, 2022, 10:07 AM
Hello Chris
Aug 11, 2022, 3:37 AM