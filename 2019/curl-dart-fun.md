Date: 8/4/2019

# Fun with Curl and Dart
<img src="http://sellsbrothers.com/public/post-images/2019-08-04-curl-dart-fun/curl-logo.png" class="main-blog-image" style="width: 250px" />

If you're a Dart programmer, [the curl command](https://curl.haxx.se/docs/manpage.html) doesn't really help you. Oh, it can tease you with of its wonderful functionality, but you still have to take anything you can do with curl and manually translate it into Dart code.

Until now.

## Updating curl.trillworks.com for Dart
For almost 5 years now, [Nick Carneiro](https://github.com/NickCarneiro) has maintained a wonderful site for converting curl commands into network code for your favorite language: curl.trillworks.com.

<img src="http://sellsbrothers.com/public/post-images/2019-08-04-curl-dart-fun/curl.trillworks.com.png" />

I guess his first most favorate language must be Python, since that's the default, but since last week, there's been a new language listed: Dart. In addition to providing this site, Nick did one other thing: he exposed the underlying tech as an OSS repo on GitHub: [curlconverter](https://github.com/NickCarneiro/curlconverter). And that repo is a wonder to behold, because it was factored in such a way that I could add Dart generation support for Dart in a weekend. Truly an example for other multi-language tools to follow.

## Chrome DevTools: Network
The reason you care about turning curl into Dart is because the Chrome DevTools turn the curl command format into a universal capture format for requests in Chrome. That means that you can go to your favorite web site, dig around on [the Network tab of the Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/network/), right-click on the bit that you like and pull it out as a curl command.

<img src='http://sellsbrothers.com/public/post-images/2019-08-04-curl-dart-fun/copy-as-curl.png' />

When you do, you'll end up with something like this:

```shell
curl "https://www.fantasynamegen.com/barbarian/short/" -H "Upgrade-Insecure-Requests: 1" -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36" -H "Referer: https://www.fantasynamegen.com/" --compressed
```

Even if you never run this as a curl command, you can still use it to generate code for your language of choice.

## Dart networking code
Pasting the curl from Chrome DevTools into [Nick's curl converter website](https://curl.trillworks.com/#dart) gives you Dart code that looks like this:

```dart
import 'package:http/http.dart' as http;

void main() async {
  var headers = {
    'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36',
    'Referer': 'https://www.fantasynamegen.com/',
    'accept-encoding': 'gzip',
  };

  var res = await http.get('https://www.fantasynamegen.com/barbarian/short/', headers: headers);
  if (res.statusCode != 200) throw Exception('get error: statusCode= ${res.statusCode}');
  print(res.body);
}
```

This is actually a complete program and if you put it into a file, e.g. fanname.dart, you're almost ready to run it. You also need a minimal pubspec.yaml that looks like this:

```yaml
# pubspec.yaml
name: fanname
dependencies:
  http:
```

The pubspec file lists the name of your app and it's dependencies, specifically the http package. These two files together are enough to run the program and get the same output as the curl command:

```shell
$ pub get
$ dart fanname.dart
```

The call to ```pub get``` pulls down the dependencies and the call to dart runs your program. Of course, this assumes that you've got [the Dart SDK](https://dart.dev/tools/sdk) installed and on the PATH. Assuming you do, you'll have output that looks like this:

<img src='http://sellsbrothers.com/public/post-images/2019-08-04-curl-dart-fun/curl-dart-out.png' />

Actually, the output in this case goes on for a lot longer than that, so it isn't particularly helpful, but with a little filtering, it can be:

```shell
$ dart fanname.dart | grep '<li>' | sed 's/<\/*li>//g'
Vadryt
Hildecon
Axra
Rordryt
Freyagar
Egelkele
Mand
Sigceo
Krokrolm
Ric
Vase
Hildekrucen
Rabeorth
Thakald
Morncrom
Lafthe
Garrak
Caror'n
Theodtarg
Ordhall
```

## Where are we?
If the network code you want is already being executed via curl or on the web, that means you can take that curl command and turn it into Dart code with zero effort on [curl.trillworks.com](https://curl.trillworks.com/#dart). The code from that site can be executed just like a curl command immediately or used as the start of your own code.

The example I was showing in this post generated HTML, but if instead the result was JSON, then you can take that JSON output and paste it into [quicktype.io](https://app.quicktype.io/) to get the JSON serialization/deserialization code for Dart, too. It's a great time to be a Dart programmer!