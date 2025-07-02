Tags: flutter

# Dart and Flutter packages need your Love!

<img src="/public/post-images/dash-love.png" class="main-blog-image" style="width: 250px" />

Do you have some free time? Are you excited to get involved in the Dart and Flutter ecosystem? Then have I got a deal for you!

Last week, the Founder and CEO of [Shorebird](https://shorebird.dev/), who is coincidentally also the Founder of the Flutter project at Google, had [this to say](https://x.com/_eseidel/status/1838789824276500661) about the current start of the state of the package ecosystem that lies at the core of Dart and Flutter:

<img src="https://sellsbrothers.com/public/post-images/2024-09-24_eseidel_x.png" />

The reason that Eric was asking for a list of "importand & abandoned" packages was so that the community could decide which packages needed some more love. Such a list is important, for example, if you're the FlutterFlow corporation and at [a recent keynote](https://www.youtube.com/watch?v=3-OggW44lHc), you've recently announced that you have $1M to contribute to the Flutter community (thanks [Axel](https://www.linkedin.com/in/asgreaves/) and [Abel](https://www.linkedin.com/in/asmengistu/)!).

For the rest of us, such a list is important if we'd like to get involved with the Dart and Flutter community and pitch in on some of the packages that need a little bit of love.

Towards that end, I've produced an initial list of [the top 500 packages with a calculated "love number"](https://docs.google.com/spreadsheets/d/1j5FuDjtB7vBhqJgMs7WpY5_W8ZTSm0LwUv92LoepCE8), which is equal to the number of days since publication divided by the popularity. This should really be called the "needs love number," since the higher the number, the more a package needs love.

<img src="https://sellsbrothers.com/public/post-images/2024-09-30-publove.png" />

I've got two ranges of numbers that sometimes cross into the red zone in this spreadsheet. The first is the number of days since publication, which is marked in red if the package hasn't been published within the last 6 months. Similiarly, if the love # is >225 (180 days/80% popularity), I mark it as red.

## provider & permissions_handler: have love

You can see several packages marked as red in this screenshot, the first being the `provider` package. The `provider` package has a good excuse for not being updated recently -- the author has decided that he'd rather people use the `riverpod` package as a replacement. That's fair and it says so in the notes (and you can suggest notes for packages you have info about [in the publove repo](https://github.com/csells/publove/blob/main/packages/publove_lib/lib/src/package_notes.dart)). Even so, Remi is keeping `provider` relatively up to date, as evidenced by the fact that his love # is still in a good range. Likewise with `permission_handler` from baseflow is also still in a good range. Thanks to both of you for the great work!

## font_awesome_flutter: has love

The `font_awesome_flutter` package also has a red love #. So is it in trouble? Let's look at the repo:

* Issues: [8 open issues](https://github.com/fluttercommunity/font_awesome_flutter/issues?q=sort%3Aupdated-desc+is%3Aissue+is%3Aopen) and 189 closed
* PRs: [3 open PRs](https://github.com/fluttercommunity/font_awesome_flutter/pulls?q=sort%3Aupdated-desc+is%3Apr+is%3Aopen) and 67 closed

Those are pretty respectable numbers, so I'd say that the folks at fluttercommunity.dev are doing pretty well with it. Thanks!

## google_sign_in: needs love!

On the other hand, the Flutter team itself isn't able to always keep up with demand. For example, the `google_sign_in` package has a love # of 291:

* Issues: [69 open issues](https://github.com/flutter/flutter/issues?q=sort%3Aupdated-desc+is%3Aopen+is%3Aissue+label%3A%22p%3A+google_sign_in%22+) and 197 closed
* PRs: [3 open PRs](https://github.com/flutter/packages/pulls?q=sort%3Aupdated-desc+is%3Aopen+label%3A%22p%3A+google_sign_in%22) and 147 closed

It does seem like the `google_sign_in` package would use some love. Do you have a special affinity for social auth? If so, perhaps submit a PR? You can tell by the numbers that PRs get a higher priority than just plain ol' issues.

## group_list_view: needs love!

The package in the list of the top 500 with the highest love # (1369) is `group_list_view`. Does it need love?

* Issues: [5 open issues](https://github.com/Daniel-Ioannou/flutter_group_list_view/issues?q=sort%3Aupdated-desc+is%3Aissue+is%3Aopen) and 4 closed
* PRs: [0 open PRs](https://github.com/Daniel-Ioannou/flutter_group_list_view/pulls?q=sort%3Aupdated-desc+is%3Apr+is%3Aopen) and 7 closed

Having not been published in 3.5 years and not having a verified publisher, perhaps [Daniel](https://github.com/Daniel-Ioannou) could use some help with this one, especially with a 95% popularity score and 1299 likes.

## flutter_barcode_scanner: really needs love!

Probably the hardest kind of package to keep well-maintained in a plugin, which have platform-specific code. For example, the `flutter_barcode_scanner` package has some outstanding issues:

- Issues: [188 open issues](https://github.com/AmolGangadhare/flutter_barcode_scanner/issues?q=sort%3Aupdated-desc+is%3Aissue+is%3Aopen) and 114 closed
- PRs: [21 open PRs](https://github.com/AmolGangadhare/flutter_barcode_scanner/pulls?q=sort%3Aupdated-desc+is%3Apr+is%3Aopen) and 26 closed

Even though this package has 1342 likes, it has been published in 3 years and has no verified publisher. It could clearly use some love.

## Where are we?

I pulled this list together because I want the number of Dart and Flutter packages that get regular love to increase and to squeeze the red out of this spreadsheet. If you're like to give me a hand with finding the packages that need love, feel free to lend a hand with [the publove repo](https://github.com/csells/publove). The core data all comes from [the excellent pub_api_client package](https://pub.dev/packages/pub_api_client) that [Leo](https://github.com/leoafarias) maintains w/o any docs or support from Google (thanks, Leo!). Also, if you're interested in pub.dev stats in general, I just stumbled onto the [pubstats.dev](https://pubstats.dev) site, which looks promising.

Scrolling through the publove list, there's a lot of red. All of us working together is what makes Dart and Flutter great, so if you've got some free time and there's something on this list that appeals to you, lend a hand!