---
title: Diagnosing issue with language or tooling of little experience
tags:
---
I recently added posts to my website. In the past they would show up on us.techlahoma.org but they didn't now. So I reached out to [Rob Sullivan][] to make sure my site was added. He said it was but mentioned that they rely on Google to read the feeds as a single source. That way they don't have to deal with the different formats. Makes sense. Maybe google is a bit behind on something? I decided to figure out what was going on.
<!--more-->
This is going to be a long post. It's a discovery post where I take you along as I am trying to figure something out. Nothing to really pickup specifically but just learning how someone else learns. I'm a .NET developer for the most part. The techlahoma site is a Ruby on Rails app I see first step into their github account. My experience level with Ruby and Rails is minimal so this should be fun. You will read me say things that a Ruby/Rails dev will know by heart. But just come along and enjoy the journey.





us.techlahoma.org

Ruby app.

On Github

Noticed /buzz is same as index

Looking in github repo for a route config thing. Similar item usually exists in others.

See config.ru.  Sounds like a place to start especially with the commit comment.

  -> Refers to ../config/environment.  Thinking that's not going to help me with my goal but I'll take a look.

  -> Oh, the environment file refers to an application file. THAT may be useful.

  -> application.rb was not useful. Just a bunch of requires and an empty inheriting class. But it did refer to a boot file. (Inception movie? Dream within a dream or down the rabbit hole)

  -> boot.rb is doing gem stuff. Not helpful. Bit of a dead end here. Going to look at application.rb again 

  -> Reading comments in application.rb application class. Looks like config/initializers is where I may want to go. Feels wrong to not see what's being used here. All the magic making something 'easy' but not 'simple'.

  -> Nothing in config/initializers immediately grabs my attention. I'll look at config/environments

  -> Nope, that looks like it's just different settings for local vs prod.

  -> It's only at this point that I see the obvious 'routes.rb' in config. <face palm>




routes.rb

  -> In here I'm looking for 'buzz' and for anything that points 'default' or 'index' to 'buzz'

  -> not sure what that first 'get "user_groups/index"' does at the moment. Threw me off. Thought it was defining some sort of block.

  -> 'scope :path '/buzz' ".  That's what I'm looking for. Mentions a buzz controller and then does a "get '/' => :index".  Sounds like an index method on the buzz controller.

  -> I notice an api namespace. Sounds like something to play with later.

  -> At the bottom is the alias for the root. "root 'buzz#index'. Looks like that redirects to the buzz controller index method. Sounds right.

  -> At this point I have a pretty good idea of where the call is going as well as what it may look like. Now to find that buzz controller.




Buzz controller

  -> Looking for a controller folder. Pretty common.

  -> Nothing in root

  --> Lib looks promising. assets and tasks. Nah, not the right direction

  -> Ahh sorry. app. Right at the top. Yeah I tend to glance over things from time to time.

   -> AHA. Controllers folder

  -> Two things in the buzz_controller.rb  index and 'updatethevariousblogs'. I'm guessing the second one is what I'm interested in. So now to find 'BlogRoll'. It also looks like that method returns 200 OK with 'harvest complete' in the response content. Ruby on Rails is pretty nice to read.




BlogRoll

  -> So where is 'BlogRoll'. I see a 'models' folder. Perhaps in there. I mostly think of a app 'layer' would house that but this is a simple site so it doesn't need all that formality.

  -> Yep. blog_roll.rb. Looking for 'harvest_new_entries'. Found it first method.

  -> I see the 'rollin' variable name and makes me think of Weird Al's "White & Nerdy song/video". :-) Odd how that fits so well.

  -> Looks like it's going through a list of active Blogs to harvest from. Makes sense.

  -> Next line refers to 'parse_feed_norma'. (Sidenote, Underscores? really. Such a pain to type). Also noting that it looks like for posts in the last three days maybe?

 -> parse_feed_normal uses GoogleAjax (Rob Sullivan said something about this on twitter). Something about GoogleAjax:Feed maybe?

  -> Perhaps. But it gets the articles looks like. Then checks the published_date to be within the last three days (from the parameter in the harvest method).

  -> Does a sanity check to make sure it's not already persisted. Note that I haven't even looked at that part. Don't care. I considered that handled.




Momentary recap

At this point I know a few things. The site uses Google's gem to get feed data. Looks like that handles atom and rss style feeds at the same time. Nice so you don't have to worry about that. The site also requires the 'harvest_new_entries' to be executed on a regular basis. This is exposed via 'updatethevariousblogs' rest call so perhaps it's something configured where they deployed it or they have an automated thing external doing that. Not sure but in any case I can hit that url myself possibly. I didn't recall seeing anything on authorization around that rest endpoint. Also, some of my blog posts that I want on there are more than three days old. I have a static site but hey I can mess with the published date I bet (snicker) and force it to get put on the site here. The thing I'm not 100% sure about is if my blog's feed is even in their list. Rob said it was but I'm going to try to find it myself. But I don't have access to the DB. But I can check the code for ways to verify it.




Verifying my site in the list

First I'll just look to see if they list the blogs and if mine shows up I can just look to see if they have my feed url correct.

-> Darn nothing. They have the user groups but not the individual blogs. I may add that to the site and send a PR but first things first: my posts. ;-)

-> Lets check that api or any other rest point that may give me some more detail. Start at the route.rb

-> Ahh. that api section looks very promising. Has a 'blog' scope (whatever that means) with a "get '/feed' ".  Reads like I can get the feed or at least the feed url for a particular blog.

-> And right above that is a 'docs#index' route. Perhaps that will give me a listing of blogs. I will do it straight from Chrome.

-> I try us.techlahoma.org/api first. That's the website plus the namespace. Ooh. It's returning HTML, paying attention to the accept header I see.

-> Sad face. The feed part says "Coming soon". Hmmm.

-> Let's try blog since that is defined. /api/blogs is 404.

-> Did /api/groups for a sanity check. Got json back. No accept header love here?

-> Perhaps the /api/blog needs more data. The route points to the blog controller. Oh wait, it doesn't have a "/" route defined within that scope. Just a "/feed" lets try /api/blog/feed

-> That got some json. Looking at it.

-> BINGO! This is a list of the blog feeds they have. I see that because it's an array and everything ends in some form of 'feed'.xml'.  I'm assuming that is the same list they harvest from.

-> Does my blog feed exist? Yes it does. But it's the wrong url. It's my old one. So I can fix that easily. I use hexo.io and when I added the feed capability it changed name from when I was hosting elsewhere.




--> Changing feed url <--




Done with that. I also manually changed the last three posts to have yesterday's date on it. I'll get my posts on the techlahoma site one way or another. :-)

-> Now I'll hit the 'updatethevariousblogs' endpoint and hope for the best.

-> So I'm looking at the routes.rb and notice a difference.  '/buzz' has '/' for index. But 'updatethevariousblogs'. No beginning slash. The rest of the routes have that. /blog/feed for example. Took just a minute of thinking to realize that must be a query parameter. No value just something there. I'll try that. Should expect 'harvest complete' to show up as I saw before in the code.

-> I tried that. Not what I expected back. Tried =true and =1 but not that simple response back. Just the /buzz. Wondering if auth is required. Checking that BlogRoll again

-> I don't see anything immediate on the buzz_controller. Back to routes.

-> Actually just for kicks I tried /buzz/updatethevariousblogs. It worked. I guess that '/' is optional here?

-> But my posts still don't show up. Hmmm. Let's look at that feed list again. Perphaps my blog is not active. Copy paste the url they have to see what they get...

-> They get the right stuff. Okay time to look at the code for this part. Quck look at routes.rb points me to blog_controller.feed. Fast note, in the api folder given the namespace.

-> Okay so it pulls all the blogs. Doesn't filter. Odd note. It also has that feed method in there twice.







GoogleAjax:feed

-> At this point my blog is either inactive in their list or something's up with the GooglAjax:feed usage.

-> Off to ruby gemland for googleajax. I'll guess they are using 1.0.1

-> Homepage is on GitHub. Sweet, source code. Let's find the feed.load method.

-> Lib sounds promising. Googleajax folder and feed.rb. Quickly getting this one.

-> Here's a load for standard_api. Hmm different.  class feed < Results.  That looks like an inherits chain. Lets find Results

-> Sibling file results.rb. Inherits from Base. Mental note it extends API. Keep an eye out for something relavant if needed.

-> Sibling file base.rb. Oh wait. extends API. Hmmm, wonder if that pulls in stuff from an API class. Because all that's in base is inheriting from hashtable. I know it's not going to be in there. AND the feed.rb had 'standard_api' in it. Lending to the thought of using the API. My mind works background processes while my eyes and fingers work the 'UI'.

-> Sibling api.rb file.... I'm looking for a 'standard_api' method of sorts. Found it at the bottom. Takes a method parameter and a block. I know a block is a function more or less. All that functions as objects ability.

-> The one I'm thinking of is that 'load' method and the block defined in feed.rb. Back to feed.rb

-> {|h| h['feed']}   ..... okay ..... Curly's define the block of the function. h seems to be an input parameter (feed url in this case).  But "h['feed']" ? Comments don't help here. Back to api.rb

-> standard_api does a 'get' for each method with it's block. So look at the 'get' method.

-> Ahh a comment on this get method is interesting. It sends the request to google for fulfillment. Sounds like I may be able to test this out myself via Chrome.

-> This line (#20) was weird at first

    -- GoogleAjax::get(kind, method, query, args)--

    But then I notice the 'kind' method just above the 'get' method. It does this odd bit:

     -- name.demodulize.downcase.to_sym --

     I'm not that familiar with Ruby. But 'module' makes me think of the feed.rb since that would be the one initiating this call stack. So my guess is that it will set kind (@kind) to 'feed'.

-> Looking back at the get method it looks like it might be "get(feed, load, [other stuff], the block)"

-> I was initially confused because it looked like a recursive call going to GoogleAjax::get.  But I'm In the get call. Then I remembered I'm in the API module. Need to find the root GoogleAjax module and get method.

-> Went up and up to googleajax/googleajax.rb

-> Ugh, a bunch of requires. But it extend the request so let's find that. That was in the googleajax folder.

-> That has the get method in it I'm looking for. I hope to figure out the url it creates.

-> Line #22 has what I believe to be the ticket: ("#{PATH_PREFIX}#{api}/#{method}?" some more stuff I won't look at for now.

-> PATH_PREFIX is defined on line #4 with the host just above it:

       ajax.googleapis.com/ajax/services

-> SO:  ajax.googleapis.com/ajax/services/feed/load

-> Okay so now I need to see those query parameters: It looks like is't mapping keys and values ( map{|k,v| was all I noticed immediately. Tells me enough)

-> I know what the value is, my feed url. But what's the key? Look at feed.rb again for a param name? Just says :load. That's the 'method'.

-> Okay API.rb. I'm paying attention to the 'query' parameter in the get method. That's the third param. So back to request.rb and it's query param.

-> Aha:  args = args.merge!('q' => query) on line #16. Look slike a 'q' query parameter with the url.

-> So try: ajax.googleapis.com/ajax/services/feed/load?q=http://teapotcoder.com/feed.xml

-> It downloaded a javascript file. JSONP I'm guessing. Opened it. Contents report "invalid version". So I noticed that part of the request so not a surprise. Back to request.rb to figure out the version to pass

-> Not in request.rb. Maybe it was in API.rb Yep there it is. Version is 1.0 (from the method above in that file). It also look slike th ename of the query parameter will be 'v'. Because I remember some arg merging from earlier. So let's add that.

-> ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://teapotcoder.com/feed.xml

-> OOOO I got something this time. Looks like a dump of my feed. Latest post is in there.




Momentary recap

So at this point I feel that my feed is working correctly. Google appears to be pulling that data right. So for the moment I going to lean on my blog being inactive for some reason. But I'll pass the url from above onto techlahoma. I'll post it as an issue.
