# Understanding Flutter: deep links on the web

> **NOTE: This content has been subsumed by the package I wrote to support declarative routing via the Navigation 2.0 API: [go_router](https://pub.dev/packages/go_router). This package is what I use now for my own projects and I hope that you'll find it useful for your projects as well. Enjoy.**

Lately I've been working on a little side project with a friend of mine and I'm the coder, so I get to pick the tech I want for the implementation. My choice is easy: Flutter! We're targeting the web with our project for two reasons: 1) it makes distribution easy and 2) it enables us to do social media marketing with deep links to content that gets people to come to the site, sign up, ... profit!

Of course, for that to work, deep linking has to work, which is a whole good news/bad news thing with Flutter web. The good news is: all the bits and pieces are there to enable deep linking. The bad news is, like a father in the wee hours of Christmas morning, there is some assembly required.

# The sample code

This blog post builds up a very simple app step-by-step in a way meant to take you from the basics of navigation in Flutter (which I assume [you already know](https://flutter.dev/docs/development/ui/navigation)) to the advanced considerations you'll need to take into account for your Flutter web app to work well. All of the code from this blog post is available step-by-step as commit history in [this github repo](https://github.com/csells/flutter_deep_linking). The last commit represents best practices as far as I know them.

## What is deep linking?

While mobile apps also support deep linking, for the purposes of this post, we'll focus on [the web-specific definition from Wikipedia](https://en.wikipedia.org/wiki/Deep_linking):

> "deep linking is the use of a hyperlink that links to a specific, generally searchable or indexed, piece of web content on a website (e.g. "http://example.com/path/page"), rather than the website's home page (e.g., "http://example.com"). **The URL contains all the information needed to point to a particular item.**"

It's that last bit (emphasis mine) that we care about -- a shared URL that our Flutter app can use to get the user to the appropriate content.

## Deep linking scenarios

There have been a few interesting articles lately on Flutter web as related to navigation and especially deep linking, like [Deep Linking for Flutter Web](https://medium.com/@rody.davis.jr/deep-linking-for-flutter-web-a34c2c181df0) by Rody Davis, which shows how to use [fluro](https://pub.dev/packages/fluro) ("The brightest, hippest, coolest router for Flutter") and [Flutter web: Navigating URLs using named routes](https://medium.com/flutter/flutter-web-navigating-urls-using-named-routes-307e1b1e2050) by Per Classon, a member of the Material team at Google (hi, Per!). However, while both articles are a great intro to the topic, neither of them covers everything I need to make sure that my app is a good web citizen.

If you're not familiar with the basics of Flutter navigation and routes, I recommend [the Navigation and routing page from the docs](https://flutter.dev/docs/development/ui/navigation). However, I'm interested in more advanced scenarios that have specific implications on the web:

* Navigating to sub-pages w/ parameters, e.g. `/family?fid=452` or `/family/452`
* Navigating to a sub-sub page w/ parameters and parent-child dependencies, e.g. `/family/452/person/42`
* Error handling for navigation to a sub (or sub-sub) page w/ invalid parameters
* Error handling for navigation to a page that's not there

There's a lot here, so let's dig in.

## Sample Data Model

To start, let's say we want to navigate through a hierarchy of people grouped into families:

* families
  * family
    * name=Sells
    * people
      * person: name=Chris, age=50
      * person: name=John, age=25
      * person: name=Tom, age=24
  * family
    * name=Addams
    * people
      * person: name=Gomez, age=55
      * person: name=Morticia, age=50
      * person: name=Pugsley, age=10
      * person: name=Wednesday, age=17

In Dart, this data can be represented like so:

```dart
class Person {
  final String name;
  final int age;
  Person({this.name, this.age});
}

class Family {
  final String name;
  final List<Person> people;
  Family({this.name, this.people});
}

class App extends StatelessWidget {
  static final title = 'Flutter Web Deep Linking Demo';
  static final families = [
    Family(
      name: 'Sells',
      people: [
        Person(name: 'Chris', age: 50),
        Person(name: 'John', age: 25),
        Person(name: 'Tom', age: 24),
      ],
    ),
    Family(
      name: 'Addams',
      people: [
        Person(name: 'Gomez', age: 55),
        Person(name: 'Morticia', age: 50),
        Person(name: 'Pugsley', age: 10),
        Person(name: 'Wednesday', age: 17),
      ],
    ),
  ];
  ...
}
```

This data can be arranged nicely to give us a sample to play with, e.g. a home page, a sub-page and a sub-sub-page.

![image-20200411174338110](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411174338110.png)

By the time we're done, we want to be able to be able to copy a deep link to a person and for that to navigate correctly.

## Tip #1: pass settings to MaterialPageRoute

Intuitively, because we want to navigate to sub-pages for each family and for each person, we know we have to [pass arguments to our named navigation routes](https://flutter.dev/docs/cookbook/navigation/navigate-with-arguments), as in this code.

```dart
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(title: Text(App.title)),
        body: ListView(
          children: App.families
              .map((f) => ListTile(
                  title: Text(f.name),
                  // navigate to the /family page to show a specific family
                  onTap: () => Navigator.pushNamed(context, '/family',
                      arguments: {'family': f})))
              .toList(),
        ),
      );
}

class FamilyPage extends StatelessWidget {
  final Family family;
  FamilyPage(this.family);

  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(title: Text(family.name)),
        body: ListView(
          children: family.people
              .map((p) => ListTile(
                  title: Text(p.name),
                  // navigate to the /person page to show a specific person
                  onTap: () => Navigator.pushNamed(context, '/person',
                      arguments: {'family': family, 'person': p})))
              .toList(),
        ),
      );
}

class PersonPage extends StatelessWidget {
  final Family family;
  final Person person;
  PersonPage(this.family, this.person);

  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(title: Text(person.name)),
        body: Center(child: 
                Text('${person.name} ${family.name} is ${person.age} years old')),
      );
}
```

This code shows the basics of navigation that will work just fine for navigation inside the app. Click on a family and you'll navigate to the `/family` route with that family as an argument and likewise for a person and the `/person` route. The arguments themselves are passed in a handy map.

Here's the code to create the pages with the appropriate constructor args:

```dart
import 'package:flutter/material.dart';

void main() => runApp(App());

class App extends StatelessWidget {
  static final title = 'Flutter Web Deep Linking Demo';

  @override
  Widget build(BuildContext context) => MaterialApp(
        title: title,
        theme: ThemeData(primarySwatch: Colors.blue),
        home: HomePage(),
        onGenerateRoute: (settings) {
          final args = settings.arguments as Map<String, dynamic>;
          switch (settings.name) {
            case '/':
              return MaterialPageRoute(builder: (_) => HomePage());
						// pull the family object out of the arguments object
            case '/family':
              final family = args['family'] as Family;
              return MaterialPageRoute(builder: (_) => FamilyPage(family));
            // pull the family and person objects out of the arguments object
            case '/person':
              final family = args['family'] as Family;
              final person = args['person'] as Person;
              return MaterialPageRoute(
                  builder: (_) => PersonPage(family, person));
            default:
              return MaterialPageRoute(
                  builder: (_) => Four04Page('unknown route: ${settings.name}'));
          }
        },
      );
}
```

Here we're using `settings.name` to find out which route we'd like and constructing the appropriate page widget with data from the `settings.arguments`. That widget we then pack into a `MaterialPageRoute` and return, which Flutter uses to push the route onto the stack of routes and show it. There are other ways to do routing with Flutter besides implementing `onGenerateRoute` for `MaterialApp`, but only a couple ways that allow you to to parse the arguments passed to `Navigator.pushNamed`. This is the method I like best.

In the case that there is no matching route, we show a 404 page:

```dart
class Four04Page extends StatelessWidget {
  final String message;
  Four04Page(this.message);

  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(title: Text('Page Not Found')),
        body: Center(child: Text(message)),
      );
}
```

And what's so wrong with this solution you ask? Well, for one thing, notice the address bar on the person page:

![image-20200411193430649](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411193430649.png)

That's right; there's nothing in the address bar except that base URL for the app. If it doesn't change as you go from page to page in the app, then the user has no deep link to share. Luckily, we can fix this by simply passing the `settings` object passed to `onGenerateRoute` onto to the `MaterialPageRoute` constructor like so:

```dart
onGenerateRoute: (settings) { // take the settings here
  ...
  return MaterialPageRoute(settings: settings, // and pass them here
                           builder: (_) => FamilyPage(family));
...
```

As you already know, the settings object contains the name of the route, which Flutter than uses to populate the address bar, since we've been so nice as to supply it:

![image-20200411194657671](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411194657671.png)Now we have the name of the route in the address bar as expected. However, that's still not enough for deep linking (in fact, we're just getting started).

## Tip #2: pass object IDs in the route name; not objects in arguments

If you were to simply refresh that page at this point, here's what you get:

![image-20200411195020036](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411195020036.png)

The problem, which Flutter is doing a very good job of hiding, is that we're trying to pull arguments out of a settings object that hasn't got any. Those arguments are only set when we call `Navigator.pushNamed`. When we refresh the page, Flutter is taking the URL from the browser and doing the navigation itself, which ends up in our `onGenerateRoute` implementation. However, it doesn't know anything about your app or it's data, so `settings.arguments` is going to be null. Now you're out of luck because you have no idea what data to build your page around. The only information you've got is what's in `settings.name`.

So what do you do? That's right -- pack everything you want into the route name itself.

### Time-out: A note about data models and unique IDs

If we're going to pass references to objects around as strings, we're going to need the objects to have unique identifiers:

```dart
class Person {
  final String id; // require unique id
  final String name;
  final int age;
  Person({@required this.id, this.name, this.age});
}

class Family {
  final String id; // require unique id
  final String name;
  final List<Person> people;
  Family({@required this.id, this.name, this.people});
}

static final families = [
  Family(
    id: 'f1', // provide unique id
    name: 'Sells',
    people: [
      Person(id: 'p1', name: 'Chris', age: 50), // provide unique id, etc.
      Person(id: 'p2', name: 'John', age: 25),
      Person(id: 'p3', name: 'Tom', age: 24),
    ],
  ),
  Family(
    id: 'f2',
    name: 'Addams',
    people: [
      Person(id: 'p1', name: 'Gomez', age: 55),
      Person(id: 'p2', name: 'Morticia', age: 50),
      Person(id: 'p3', name: 'Pugsley', age: 10),
      Person(id: 'p4', name: 'Wednesday', age: 17),
    ],
  ),
];
```

In this case, I simply added an `id` property to family and person objects, which sweeps many issues under the carpet. You have many questions to consider when adding unique IDs to your data model:

* **Are they globally unique?** Anything with a globally unique ID can be shared globally, e.g. YouTube videos have globally unique IDs. Every user may not have access to every object in your app's data, e.g. Google Docs all have globally unique IDs but not everyone can access every doc.

* **Are they unique to the user that owns the data?** If so, the deep linking can still useful for that user, perhaps as a favorite link in their browser.

* **Are they unique to the parent object?** In that case, the parent object must be available when looking up the object. In the case of our sample, notice that Chris and Gomez have duplicate IDs; clearly we'll need a family object around to disambiguate when looking up a person object by ID.  

* **Where do the IDs come from?** It's very easy to generate globally unique IDs; in fact, there are [several Dart packages that generate unique IDs on demand](https://pub.dev/packages?q=unique+id). However, the problem there is storage cost and long URLs. Even so, globally unique IDs solve a lot of problems and are used a lot.

  On the other hand, you may create a new object, send it the database and then let the backend assign a shorter ID that's still unique; lots of data storage solutions provide this service. The problem here is what ID do you use in the UI while you wait for the database to give you back the real ID, assuming you want to show the object prior to confirmation of synchronization with the backend.

  Or you may decide to let the client generate short but unique IDs, perhaps using max on a sequence of integers. The problem in that case is what happens in the case of a collision.

I didn't say I had any answers to these questions; only that you have to consider them...

### Time-in: passing object IDs in routes

Once have have unique IDs in your data, you can use them to pass around in your route names:

```dart
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(title: Text(App.title)),
        body: ListView(
          children: App.families
              .map((f) => ListTile(
                  title: Text(f.name),
                	// pass the family id as a query parameter
                  onTap: () => Navigator.pushNamed(context, '/family?fid=${f.id}')))
              .toList(),
        ),
      );
}
```

Here we're passing the family object's ID via a standard web-style query parameter, e.g. `/family?fid=f1`. We can do the same thing for the person, but it needs two arguments:

```dart
class FamilyPage extends StatelessWidget {
  final Family family;
  FamilyPage(this.family);

  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(title: Text(family.name)),
        body: ListView(
          children: family.people
              .map((p) => ListTile(
                  title: Text(p.name),
                  onTap: () =>
                			// pass the family id and the person id as query parameters
                      Navigator.pushNamed(context,
                                          '/person?fid=${family.id}&pid=${p.id}')))
              .toList(),
        ),
      );
}
```

Parsing them is like parsing query parameters on the web:

```dart
onGenerateRoute: (settings) {
  // split out the real route name from the args, e.g. /family and fid=f1
  final parts = settings.name.split('?');
  final args = parts.length == 2 ? Uri.splitQueryString(parts[1]) : null;
  switch (parts[0]) {
  ...
  case '/family':
      // look up the family and complain if it's not found
      final family =
          App.families.singleWhere((f) => f.id == args['fid'], orElse: () => null);
      if (family == null)
        return MaterialPageRoute(
            settings: settings,
            builder: (_) => Four04Page('unknown family: ${args["fid"]}'));

      return MaterialPageRoute(settings: settings, builder: (_) => FamilyPage(family));

    case '/person':
      // look up the family and person, complaining if either is not found
      final family =
          App.families.singleWhere((f) => f.id == args['fid'], orElse: () => null);
      if (family == null)
        return MaterialPageRoute(
            settings: settings,
            builder: (_) => Four04Page('unknown family: ${args["fid"]}'));
      final person =
          family.people.singleWhere((p) => p.id == args['pid'], orElse: () => null);
      if (person == null)
        return MaterialPageRoute(
            settings: settings,
            builder: (_) => Four04Page('unknown person: ${args["pid"]}'));

      return MaterialPageRoute(
          settings: settings, builder: (_) => PersonPage(family, person));
  ...
  }
},
```

At the top of the code, we split a URL in the form `/name?args`, e.g. `/family?fid=f1`. We switch on the route name, using the query parameters to look up the objects we need to construct the page widgets, using our 404 page to complain if we can't find them. Suddenly we've got the first glipses of deep linking!

![image-20200411210303289](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411210303289.png)

Not only have we baked the object IDs into the link but if we refresh or past the link into a new Chrome page, it will actually show the same content! Or, if you want to change the person to p2 by editing the URL directly, you'll see Morticia!

![image-20200411210446221](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411210446221.png)

If you get a little too excited and enter an ID that's not available, the app will let you know that you've stepped off the path.

![image-20200411210638111](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411210638111.png)

### Time-out: A note about the \# in the URL

By now you have noticed the # in the URL of your Flutter app, which isn't typical of most web apps. This is a special designation for the browser when it splits the URL into two sections. Section one, to the left of the #, is for the HTTP server. This is how your Flutter app gets downloaded to your users' machines.

Section two, to the right of the #, is used for client-only information, e.g. if you wanted to scroll to a specific anchor on your HTML page. [Single Page Applications](https://en.wikipedia.org/wiki/Single-page_application) (or SPAs as the cool kids call 'em) use section two data to navigate between client-side "pages" of the app without going back to the server. Since your Flutter web app is just a really fancy SPA, it uses section one of the URL to download the entire app onto the user's machine and then uses section two for navigation between "pages" as defined by Flutter. In fact, if you were to take the # out of the URL, bad things would happen, since the server won't know what the heck you're talking about:

![image-20200411223958634](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411223958634.png)

You can certainly fix this on the server with redirection, e.g. `/person` => `/#/person`. While you're at it, you'll also want to redirect 404s to your own Flutter-based 404 page. Apparently most web servers consume [Apache .htaccess files](https://httpd.apache.org/docs/2.4/configuring.html), which is supposed to make this kind of server-side forwarding easy to do. I haven't tried this myself so please let me know in the comments if this actually works. Thanks!

### Time-in: do we have deep linking yet?

At this point, you might thing that you were done with our deep linking support for Flutter web. If only that were so...

## Tip #3: construct pages with object IDs, not objects

We have a problem in how we look up objects in our code right now, although it might be hard to see:

```Dart
onGenerateRoute: (settings) {
      ...
      // objects on the network are further away than they appear...
      final family =
          App.families.singleWhere((f) => f.id == args['fid'], orElse: () => null);
      ...
}
```

The `onGenerateRoute` method is synchronous; if requires you to provide a route immediately. However, what if your object is stored on disk or on the network or needs to be requested via carrier pigeon? Our little model is simple and not representative of the real world, so it's deceptive; it's going to be more common that you'll need to load the data from somewhere when you're mapping object IDs to their respective objects. To support that case, I recommend you get into the habit of passing the object IDs to the page constructors so that the page itself can do the asynchronous lookup. That way your page can show a loading indicator as well as an error message if lookup fails. Here's a simulated example of such a thing:

```dart
class FamilyPage extends StatelessWidget {
  final Future<Family> family;
  FamilyPage(String fid) : family = _load(fid); // construct with an object ID

  // load object by id
  static Future<Family> _load(String fid) async {
    // simulate a network lookup...
    await Future.delayed(Duration(seconds: 3));

    final family = App.families.singleWhere((f) => f.id == fid, orElse: () => null);
    if (family == null) throw 'unknown family: $fid';

    return family;
  }

  // funky FutureBuilder code to show three states: loading, failed, succeeded
  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(
          title: FutureBuilder<Family>(
            future: family,
            builder: (_, snapshot) => snapshot.hasData
                ? Text(snapshot.data.name)
                : snapshot.hasError ? Text('Page Not Found') : Text('Loading...'),
          ),
        ),
        body: FutureBuilder<Family>(
          future: family,
          builder: (_, snapshot) => snapshot.hasData
              ? ListView(
                  children: snapshot.data.people
                      .map((p) => ListTile(
                          title: Text(p.name),
                          onTap: () => Navigator.pushNamed(
                              context, '/person?fid=${snapshot.data.id}&pid=${p.id}')))
                      .toList(),
                )
              : snapshot.hasError ? Text(snapshot.error) : CircularProgressIndicator(),
        ),
      );
}
```

The code to load the family object is just like before, except we're doing it asynchronously to simulate a networked environment. If there's an error, we throw it as a string, which is caught by `FutureBuilder`. The `FutureBuilder` code is funky because we're using the result of our asynchronous loading in two places (the appbar's title and body) and because we're dealing with three states: loading, failure and success, all of which result in three different UIs, e.g. here's the one for loading:

![image-20200411220419590](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411220419590.png)

Notice, however, that this is an environment in which we can take our sweet time loading data and giving the user progress indication, error messages, etc. This is unlike the `onGenerateRoute` method, which must return synchronously (and can now be greatly simplified):

```dart
onGenerateRoute: (settings) {
  final parts = settings.name.split('?');
  final args = parts.length == 2 ? Uri.splitQueryString(parts[1]) : null;
  switch (parts[0]) {
    case '/':
      return MaterialPageRoute(settings: settings, builder: (_) => HomePage());

    case '/family':
      // pass the family object ID and let the page look it up 
      return MaterialPageRoute(settings: settings, 
                               builder: (_) => FamilyPage(args['fid']));

    case '/person':
      // pass the family and person object IDs and let the page look them up 
      return MaterialPageRoute(
          settings: settings, builder: (_) => PersonPage(args['fid'], args['pid']));

    default:
      return MaterialPageRoute(
          settings: settings,
          builder: (_) => Four04Page('unknown route: ${settings.name}'));
  }
},
```

Now would be a good time to think about using [the repository pattern](https://deviq.com/repository-pattern/) in your code so that you can abstract away the data loading, use in-memory caching, etc. but that's beyond the scope of this post.

At this point, we've got the basics working -- you have a link that you can provide to your app that provides enough information for loading objects for each of your app's pages. However, because we're using a flat namespace for our URLs, the navigation may not work the way you expect.

## Tip #4: use nested routes to build a stack

You may not have realized it, but every time you press Refresh on one of your app's pages or press Enter inside the address bar (even if you don't change the URL), you're actually reloading your app, i.e. starting over from `main`. If you're on the home page when that happens, then you reset the browser and app navigation stack of your app to have one entry:

| Navigation Stack | Index     | Route |
| ---------------- | --------- | ----- |
| Home page        | 0 (shown) | /     |

Notice the lack of back button in your app in this case:

![image-20200411222952342](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200411222952342.png)

If you click on the list entries to navigate to the person page, thus also going through the family page, you'll have a navigation stack with three entries:

| Navigation Stack | Index     | Route                   |
| ---------------- | --------- | ----------------------- |
| Person page      | 2 (shown) | /person?fid=xxx&pid=yyy |
| Family page      | 1         | /family?fid=xxx         |
| Home page        | 0         | /                       |

However, if you do a deep link in a new browser instance to the person page, you're getting a stack with just two entries in it:

| Navigation Stack | Index     | Route                   |
| ---------------- | --------- | ----------------------- |
| Person page      | 1 (shown) | /person?fid=xxx&pid=yyy |
| Home page        | 0         | /                       |

This leaves you in a strange place: if you navigate to the person page normally, pressing back gets you to the family page. However, if you just start from scratch with a deep link to the person page, Flutter's back button takes you to the home page. You might ask: where did the family page go when I do deep linking?

A better question: why the heck is the home page there in the first place?!

The answer is simple: so that you have a way to get back to the home page instead of being stranded in the middle of the app with no way (sic) home.

The real question: *how* does the home page get onto the navigation stack?

At this point, we've actually found Flutter's support for deep linking (yay!) When you start your app, Flutter (web or mobile or desktop or projected on the insides of your eyelids) will look for a route name from your `MaterialApp` `initialRoute` property. If it's `null` (and it should always be `null`), then Flutter will get it's initial route from the launching host. As I mentioned, Android and iOS both also have the concept of deep linking and this is how those are handled. In the case of the web, that initial route will come from the address bar. This is the behavior we want, which is why we should leave `initialRoute` as `null`.

If the initial route starts with a `/` then Flutter will start building a stack of routes, using `/` as the delimiter. For example, if we have the deep link `/one/two`, then it's going to build a navigation stack like the following:

| Navigation Stack | Index     | Route    |
| ---------------- | --------- | -------- |
| Two page         | 2 (shown) | /one/two |
| One page         | 1         | /one     |
| Home page        | 0         | /        |

This is what we want: you should use slashes in your routes so that the deep linking behavior you get is the same as the behavior you get when you're navigating normally in your app. In the case of our sample, that means our person route really needs to change to use a `/` instead of the `&`, i.e. `/person?fid=xxx&pid=yyy`  =>  `/family?fid=xxx/page?pid=xxx`.

Composing the route in the new form is easy:

```dart
Navigator.pushNamed(context, '/family?fid=${snapshot.data.id}/person?pid=${p.id}')
```

However, it's parsing it that gets a little trickier, since we're no longer looking for a route name that starts with `/person` but rather one that looks like `/family(something)/person(something)`. The way to solve this problem is, of course, with [regular expressions](https://blog.codinghorror.com/regular-expressions-now-you-have-two-problems/):

```dart
onGenerateRoute: (settings) {
  final name = settings.name;

  // start from most specific first
  if (name.startsWith('/family') && name.contains('/person')) {
    final re = RegExp(r'\/family\?fid=(?<fid>[^\/]+)\/person\?pid=(?<pid>[^\/]+)$');
    final match = re.firstMatch(name);
    if (match != null) {
      final fid = match.namedGroup('fid');
      final pid = match.namedGroup('pid');
      return MaterialPageRoute(settings: settings, builder: (_) => PersonPage(fid, pid));
    }
  }

  if (name.startsWith('/family')) {
    final re = RegExp(r'\/family\?fid=(?<fid>[^\/]+)$');
    final match = re.firstMatch(name);
    if (match != null) {
      final fid = match.namedGroup('fid');
      return MaterialPageRoute(settings: settings, builder: (_) => FamilyPage(fid));
    }
  }

  if (settings.name == '/') {
    return MaterialPageRoute(settings: settings, builder: (_) => HomePage());
  }

  return MaterialPageRoute(
      settings: settings, builder: (_) => Four04Page('unknown route: ${settings.name}'));
},
```

Instead of just blindly splitting the name looking for arguments, we have more complicated route names now, so we have to have a more nuaced way of recognizing them. Further, to reduce the complexity just a little, we now check the route names from more specific to more generic, which specifically allows us to look for `/family` routes with a simple use of `startsWith`, safe in the knowledge that if there was a `/family/person` route to be found that it would have been handled earlier in the code.

However, with that bit of code, now our deep link to `/family?fid=xxx/person?pid=yyy` gives us the same navigation stack as if we'd navigated there interactively in our app. In the case of deep linking, the Flutter back button on the AppBar is different from the back button on the browser.

In all cases, the browser back button will take us back to the page where we just were, so if we paste a deep link into a new browser window, the browser back button will take us back to the blank Chrome page -- it's only seen one of the Flutter app pages, so it's got just one in it's stack.

On the other hand, during the deep linking process, Flutter has built up a stack of routes as deep as appropriate based based on your initial route. When you press the Flutter back button, you'll get to pages you might not have seen yet from that stack.

This might be confusing to the user, but it seems the most useful blending of the two worlds; the browser always takes you back to the last page you saw and the Flutter back button takes you to the logical page on the stack so you can always get back to home in your app. win-win.

**And with that, we've finally in the state we want to be in; we have achieved deep linking in our Flutter web app!**

## Bonus Tip #1: better code through abstraction

You may look at the complicated logic for string matching and formatting littered throughout the sample code and think to yourself, [there has to be a better way](https://www.youtube.com/watch?v=wwROPN3Fir8)! In fact, there are [several Flutter routing helper packages on pub.dev](https://pub.dev/flutter/packages?q=routing) that you may want to check out. Myself, I didn't understand what they were doing or why I cared until I dug this deep (sic) into the topic myself (and hence this blog post). That said, now that you understand it, too, you can build yourself a little helper that does the job for you.

```dart
abstract class Router {
  // derived classes implement these methods
  bool matches(RouteSettings settings);
  MaterialPageRoute route(RouteSettings settings);

  // helper for 
  static Route<dynamic> onGenerateRoute(RouteSettings settings) {
    final router = routers.firstWhere((r) => r.matches(settings), orElse: () => null);
    return router != null
        ? router.route(settings)
        : MaterialPageRoute(
            settings: settings, 
            builder: (_) => Four04Page('unknown route: ${settings.name}'));
  }
  
  static final routers = [
    // start with most specific one first
    PersonPageRouter(),
    FamilyPageRouter(),
    HomePageRouter(),
  ];
}
```

Here I've build a little `Router` helper class. It's meant to be the base for every page-specific router in my app, e.g. a `PersonPageRouter` knows how to route to a `PersonPage`. The `matches` and `route` methods are for the derived class to implement. Further, the `Router` provides an implementation of `onGenerateRoute` based on the list of routers it also contains.

The magic is in the `matches` and `route` method implementations, like this one for the `FamilyPageRouter`:

```dart
class FamilyPageRouter extends Router {
  static final _routeRE = RegExp(r'^\/family\?fid=(?<fid>[^\/]+)$');

  @override
  bool matches(RouteSettings settings) => settings.name.startsWith('/family');

  @override
  MaterialPageRoute route(RouteSettings settings) {
    assert(matches(settings));
    final match = _routeRE.firstMatch(settings.name);
    return match == null
        ? null
        : MaterialPageRoute(
            settings: settings, builder: (_) => FamilyPage(match.namedGroup('fid')));
  }

  static Future<T> navigate<T>(BuildContext context, Family family) =>
      Navigator.pushNamed<T>(context, '/family?fid=${family.id}');
}
```

The `matches` method simple asks the router if the route name is a match. If it is, it asks the `route` method to parse the route name and produce the appropriate route. Notice also that if it can't find the information it needs to build a route, e.g. no family object ID, it returns `null`. Flutter will use this to abort the routing, which is a handy way to stop the user from going anywhere with a malformed URL.

In addition, each router provides a static method called `navigate` that takes the appropriate arguments to be able to push the correctly formatted named route. This allows us to avoid sprinking that knowledge all over your code. Instead, to route to the family page app, you simple have to do the following:

```dart
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(title: Text(App.title)),
        body: ListView(
          children: App.families
              .map((f) =>
                  ListTile(title: Text(f.name), 
                           // navigate to the page for the selected family
                           onTap: () => FamilyPageRouter.navigate(context, f)))
              .toList(),
        ),
      );
}
```

With this bit of refactoring, implementing the routing for the MaterialApp is very simple:

```dart
class App extends StatelessWidget {
  static final title = 'Flutter Web Deep Linking Demo';
  static final families = ...;

  @override
  Widget build(BuildContext context) => MaterialApp(
        title: title,
        theme: ThemeData(primarySwatch: Colors.blue),
        home: HomePage(),
        // all of your navigation is handled here
        onGenerateRoute: Router.onGenerateRoute,
      );
}
```

Adding new routes is now a matter of defining a new `Router` implementation and adding it to the list of routers. Easy Peazy.

## Bonus Tip #2: slash-only URLs without parameter names

Since we're now using slashes to separate sub-pages, notice that our URL is verbose and redundant: 

`/family?fid=xxx/person?pid=yyy`

It's pretty clear simply by the placement which is the family ID and which is the person ID. For that reason, many apps prefer a syntax like this:

`/family/xxx/person/yyy`

The trick here is that Flutter is going to attempt to get a route for each of the segments, even though segments like `/family` without the family ID don't make any sense. However, so long as you don't return `null` for the first one (home) and the last one (the person page in our case), it's ok to return a `null` in the middle and Flutter will not place them onto the navigation stack. To enable this bit of magic, we want our routers to produce routes like the following:

| Navigation Stack | Index     | Route                                |
| ---------------- | --------- | ------------------------------------ |
| Person page      | 2 (shown) | /family/xxx/person/yyy => PersonPage |
| n/a              | n/a       | /family/xxx/person => null           |
| Family page      | 1         | /family/xxx => FamilyPage            |
| n/a              | n/a       | /family => null                      |
| Home page        | 0         | / => HomePage                        |

With our little router helper, we can get the slash-based URL formatting we want by simply changing the regular expression and the route name to use a slash instead of `?fid=`:

```dart
class FamilyPageRouter extends Router {
  // use / instead of ?fid=
  static final _routeRE = RegExp(r'^\/family\/(?<fid>[^\/]+)$');

  @override
  bool matches(RouteSettings settings) => settings.name.startsWith('/family');

  @override
  MaterialPageRoute route(RouteSettings settings) {
    assert(matches(settings));
    final match = _routeRE.firstMatch(settings.name);
    return match == null
        ? null
        : MaterialPageRoute(
            settings: settings, builder: (_) => FamilyPage(match.namedGroup('fid')));
  }
  
  static Future<T> navigate<T>(BuildContext context, Family family) =>
      // use / instead of ?fid=
      Navigator.pushNamed<T>(context, '/family/${family.id}');
}
```

In the case of `/family`, which should return `null` (because it has no family ID), the trick is the combination of the `matches` method, which knows that this route is meant to be handled in this router, and the `route` method, which sees that the data in `/family` isn't enough to actual produce a route, so returns `null`. The person page works the same way, resulting in the following:

![image-20200412010641533](http://sellsbrothers.com/public/post-images/2020/flutter-web-deep-links/image-20200412010641533.png)

Our new deep linking format produces the navigation stack we want. And, with that earlier bit of refactoring magic and the updated router implementations, we can decide to format the route names however we like without having to change any of the rest of the app's code.

## Where are we?

Adding deep linking to your Flutter app comes with considerations about how to format route names to get a navigation stack that makes sense to your users. For Flutter on the web, we have to pile on the fact that users can enter URLs directly into the address bar and share the URLs far and wide; this is often something we want (let's go viral!), but comes with more considerations, including making sure that the route name shows up in the address bar, handling malformed URLs and packing object IDs into the route name itself. This in turn has implications about how to structure the data model and where and how to load objects asynchronously, if needed.

Flutter for web is relatively new and still in beta. The needs of web have pushed on Flutter at it's core, including a new Navigation design proposal that should make it easier to do the right thing when it comes to routing in your app, whether it runs on the web or not. Regardless, by understanding what's going on and following the best practices outlined above, you can build great Flutter apps on the web right now!