Date: 9/13/2015
Tags: colophon

Moved to Blot
=============

<img src="http://csells.blot.im/public/post-images/blot-logo.png" class="main-blog-image" />

You're reading this on the 5th version of my blog.

Some History
------------

The [first](</12641>) was a set of static text files I managed in FrontPage. The
editing was nice (once it get the HTML-on-the-disk problems right), but I
dropped everything into the same file, using anchor tags to separate posts in
one giant file, which didn’t scale.

The [second version](</12642>) was ASP.NET code to pull in my static content (I
did not want to give up my FrontPage) and arrange it into a nice layout.

The third version has been lost in the mists of time.

The [fourth version](</12670>) was a complete rewrite in ASP.NET using SQL
Server as the backing store. The bulk of the content was in SQL Server as either
HTML fragments or image blobs, requiring me to implement a blogging API (I was
way into AtomPub at the time, so that’s the one I implemented). Along the way, I
[moved this version of the site to Azure](</12731>).

The good news is that Windows Live Writer worked very well with this site (even
better than FrontPage!). The bad news is that it was the only editor that did,
it requires Windows and we no longer live in a single OS world. I want to write my
blog in Markdown from OSX or even my phone.

So, the fifth version my site, the one you’re looking at now, is running on
[Blot](<http://blot.im>). It was the video of the workflow on the home page that
really did it for me. I saw that, paid my $20 and have been spending weekends
exporting the data from my old blog ever since.

The Beauty of Blot
------------------

Here’s what I get by moving to Blot:

-   A **Dropbox-based** file management system with complete flexibility to
    arrange things how I like. Blot just takes what I give it and uses it to
    produce my blog. And I don’t need to maintain certs to protect my writeable
    AtomPub endpoint anymore, either. Blot and Dropbox use OAuth2 for such
    things and leave me out of it.

-   A **mix of HTML and Markdown content**, which let me dump all of my old HTML
    fragment-based content into Dropbox but still letting me write new content
    in Markdown with whatever editor I feel like, including one hosted on
    Windows, two hosted on the Mac and one that runs on my phone.

-   A **live preview** that’s updated every time I save. I get a preview by
    prefixing the post filename with “[draft]”, which produces a cooresponding
    “[preview]” file that matches the styles on my site. When I’m ready to
    publish, I remove the “[draft]” prefix and it’s live with a date that
    matches the first time that Blot saw this post. Or if I want to provide my
    own date in the future, I can easily do so with a bit of metadata in the
    file. Easy peasy.

-   Integration with **Disqus**, where I keep my comments. Not only do new posts
    get Disqus comments, but I was able to drop in a bit of metadata to point to
    the existing comments for my existing posts.

-   Direct integration with my existing **Google Analytics** account by simply
    providing my ID.

-   **Forwarding of old URL patterns** to their new spot on my new site, so I’m
    not contribuing to the "dead web."

-   Several Blot templates provide **mobile-friendliness out of the box** (as
    defined by [Google mobile friendliness test
    tool](<https://www.google.com/webmasters/tools/mobile-friendly/>)), which is
    handy so that mobile searches continue to find things on sellsbrothers.com
    without bias. This saved me from having to figure out the issues with the
    old site.

-   **Fabulous support** from David, the proprietor of Blot. I don’t think he’s
    had anyone drop 20 years of blog content into Blot all at once before, but
    he was super responsive, fixed all of my issues and even added some features
    just to support my scenarios. Blot is worth it just for David.

-   I get the **piece of mind** knowing that all of my content is in Dropbox, so
    if Blot goes away in another 20 years, then I have confidence that I’ll be
    able to take my content and drop it somewhere else or even build my own
    host. Also, while I’m not quite 100% confident in Dropbox’s ability to keep
    all of my files for all time, I’m got [the whole thing checked into
    GitHub](<https://github.com/csells/sb-blot>), too, which Blot has no issues
    with.

-   And I get all of that for **$20 a year**! That’s a bargain and a half.

Not quite everything
--------------------

While Blot does let me drop in JavaScript and do dynamic things, it’s not a
place to host server-side code. So, I rebuilt tinysells.com (the URL forwarding
service that I built for my books) and moved it to the [Google Cloud
Platform](<http://cloud.google.com>). It’s about [six lines of Python
code](<https://github.com/csells/tinysells/blob/master/tinysells.py>) if you’re
interested (not counting the hardcoded data which I’m sure I’ll move someday).

Also, while Blot has a full set of fully customizable templates, none of them
was quite what I wanted and my hacking has left me vaguely unsatisfied. If
anyone has design advice, I’d sure love to hear it!

Where are we?
-------------

I imagine that I would’ve been just as happy with WordPress as Blot, but David
does such a good job with Blot that I couldn’t say “no." Certainly I’ve blogged
more in the last week than I have since joining Google, so I’ll take that as a
good sign. Blot seems to have inspired me; what more could you ask from a blog
service than that?
