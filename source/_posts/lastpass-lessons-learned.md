---
title: LastPass Lessons Learned
date: 2014-04-11 00:00:00
tags:
---
I really like using [LastPass][]. In the wake of the [HeartBleed][] fiasco I knew I should change several of my passwords as should you.<!--more-->Just make sure the site you are changing your password on has fixed their certificate or you will still be open to the same vulnerability. Anyway the reason for this post is because I decided to change my **master** password on [LastPass][] last night about 10pm. In the back of my mind I was thinking this was a bad idea and should do it in the morning. But no fear I'm good with my passwords, I have a system.

## It. Failed.

First thing to clear up. I don't use passwords. I use pass**phrases**. That is my 'password' is a longer string of text more akin to a sentence with stuff thrown in. See the [XKCD][] for an idea. Maximum password lengths of 20 or less really bug me. Also [LastPass][] does not have access to your password and cannot reset it to a dummy value. All the encryption happens on your computer and then passes the encrypted data up to their servers. They couldn't open my data if they or anyone else forced them to. That's one of the top reasons I picked [LastPass][].

So last night I change my pass**phrase** feeling good about protecting my data. This morning I login to [LastPass][] and recive the 'invalid login' message. No fear it happens to me on occasion with the longer passwords I hit the wrong key. Try again and it should be fine. Nope. Slloowwly try again finger pecking this time, nothing. Now I'm officially sweating and co-workers are asking if I am okay. I start making a list of possible alternatives I may have inadvertently used last night. Casing, spacing, number, punctuation you name it I tried it. I was about to give up hope. But [LastPass][] sent me my hint as I had requested it and it had a link to 'Account Recovery'.

And there in lay rescue. The last ditch effort that [LastPass][] offers is to revert your vault. This is truly a final option and a severe one depending on your situation. It just so happens to be one I was hoping that they would have. The reverting your vault option will restore a backup of your vault to the one just prior to the last master password change. It's very effective for just my situation, having changed my password but done forgot it. Click revert; wait for the server to rescue my data and voila! Login with my old password and everything is right as rain again.

Just be sure to have access to your email account that your [LastPass][] account is setup to send security emails too. Fortunately I don't use [LastPass][] to login to that particular email address to force me to have that password in mind all the time. For those that are concerned about the revert capability you can turn it off in your account settings.

Again, I really enjoy the [LastPass][] service. But I need to get a backup plan in place for this data just in case I totally lose my memory.

[LastPass]:https://www.lastpass.com
[HeartBleed]:http://heartbleed.com/
[XKCD]:http://xkcd.com/936/
