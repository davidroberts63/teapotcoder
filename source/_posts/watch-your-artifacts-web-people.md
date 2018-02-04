---
title: Watch your artifacts web people
date: 2015-11-01 21:55:37
tags:
---
I just switched to using [hexo.io][] for my personal blog. But something was bugging me. A static site taking six to eight seconds to load? Must fix.
<!--more-->
At first I thought it was just because I was using the `hexo server` command to test the site locally. But upon first deploying I still saw the same 6+ second delay in the site completely downloading. I pull up the developer console (happen to be using Edge which is very nice by the way) and look at the network activity of the site. Look at this (removed a few columns for space):

![](/images/long-load-time-network.png)

CSS and font files? On a CDN? Taking a total of about six seconds to load? Taking a bit of a deeper look into what the Edge browser is telling me I notice those blue bars on the right. Most of the time connecting to `*.useso.com` is just connecting. **Nothing** is going on. Not sending the request or downloading a response. Just waiting for a connection to do anything.

I needed to figure out where the request was being initiated from. At first I thought it was something in my template. Sort of. Searching for `fontstatic` yielded nothing. But I did find some `useso.com` stylesheets references. If you look at the network activity again you'll see under the `Initiator/Type` it says 'style' for some of those font files. That means a style sheet is specifying the url. The other top ones are from 'localhost' (testing the site) so it's directly from my HTML. If you follow the path (mentally speaking):

1. Browser loads HTML
2. HTML specifies stylesheet
3. Browser loads stylesheets (top three from above)
4. Stylesheet specifies font url
5. Browser loads fonts (next five from above)
6. Visitor closes page because my blog takes to long to load.
7. Nothing here to see.

So if I remove the stylesheet reference I solve the problem. But I need the stylesheet right? It came with the template. Unfortunately many of us have tendencies to [stick with the default][]. Well I decide to see how it will affect the page look and feel. I find the stylesheet reference to anything on useso.com and remove them:

```
<link href="//fonts.useso.com/css?family=Source+Code+Pro:400,700" rel="stylesheet" type="text/css">  
<link href='//fonts.useso.com/css?family=Open+Sans:300,600' rel='stylesheet' type='text/css'>  
```

Those two reference match the first two requests. Run the site again loading the currently deployed site side by side against my local one and...

Nothing

Not one single difference that is visible or noticable. There are other fonts being used; font awesome for instance. But those two references add no value to my site. They only cost it time. Checked the page load time now and it dropped by a full four seconds. Okay, so not the six seconds to 110 milliseconds I mentioned on twitter.

{% blockquote @davidroberts63 https://twitter.com/davidroberts63/status/660935770320990208 %}
Page load time: 6 seconds. Remove two CDN css links that use about 4 fonts.
Page load time: 135 milliseconds
Watch your artifacts people
{% endblockquote %}

But it wasn't until I started writing this that I realized I was comparing network activity with an empty cache to one that had a full cache. Still three seconds is significant. Especially all that connecting time. I still don't know what that's all about. Any connections to *.useso.com did that. None of the others showed that kind of waiting to connect; including google analytics and disqus.

To make a long story short; I did some optimization of my static blog because I thought it was taking to long to load (still is). But I did it after I got it working. When I compared the before and after of the removal I knew that I wasn't going to have to put it back in later on. I didn't accept the defaults and took a deeper look to make adjustments. But I didn't go crazy right yet. I will still look into some bundling of stylesheets. Watch what you (and others) put into your websites.

[hexo.io]:https://hexo.io/
[stick with the default]:http://teapotcoder.com/post/net-default-syndrome