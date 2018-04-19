---
title: Failures
date: 2014-02-01 00:00:00
permalink: page/failures/
---
The most important thing to do when you fail is to learn from that failure. If you go through life without failing then somehow you've come into this world with all the knowledge and experience of the universe. I'm pretty sure none of us have that. So we must fail. We must get back up. We must learn. We must try again.

This is **not** an exhaustive collection of my failures. I'll add to it as I am able to put into words other failures that have occurred as well as new failures that come my way.
* * *

### Access db in a db
My first real project at my first real software development employer was to create an application that let them track some assets such as vehicles, printers, computers, toolboxes, etc.
I did it in Access back in 2000.
I effectively made a database inside a database. _Note to all, bad idea._
I made it so flexible that you would be able to track just about anything except stocks and bonds (not likely to do that anyway). But at the same time it was nearly unusable because of all the setup that they would have to do for it.
They never used it. One of my co workers said
> It was like a beautiful gem encrusted gold box with velvet interior and a hinge that didn't work.
##### What I learned
* Don't go overboard with your projects. All those fancy ideas you have put them in a binder or some notes and set it to the side. When you get the project out in use then get those fancy ideas and try them out. But make sure what you build first is usable.
* Use the right tool. Making an Access App was not the correct tool for something like this. There were additional sync requirements that I had to go deep into VBA to get to work in Access and it was not pleasant. It was all a lot easier in VB.NET with an Access db as the data store.

My second round with that app was wonderful. Much more limited but much easier and faster to use. It was done in VB.NET and they loved it. Used it almost everyday.
* * *

### It's not my job
I was working on a project on location while a manager came in talking about other parts of the business that needed to get taken care of. He jokingly suggested that I help out. I responded with "That's not my job". Boy was that the wrong answer. I ended up helping out a lot more than he was joking about.

##### What I learned
* It is your job. You work for that client or employer. It is your job to help make them successful in any way possible. If you don't have the expertise in an area then think about how your skills can be used. Perhaps you can help them identify anything they may have missed (analytical) or help mediate a discussion if you have good inter-personal/soft skills. It's up to the client to decide if it's worth their time and money for you to help out in an area.

* * *

### Working as an independent developer
After six and a half years at my first employer I decided to leave and do some independent development work. It was primarily with one major contract work provider using a language called [PL/B][]. I lasted about eight months then went back to full time work.

##### What I learned
* I don't like the PL/B language. Nothing against the folks that keep it going. They do a wonderful job, in fact it's getting .NET integration soon (if not already complete). I'm glad that I did it though, it was a great learning experience for myself and allowed me to know for certain that I don't like it. I jumped into it because the first foray into it was short and not enjoyable but I decided to take this plunge to force myself to give it a full fair shot and that I did.

* Working for yourself takes a large amount of discipline and organization. 1) I cannot work within eyeshot of my bedroom or living room. I'll feel like I'm always 'at work'. A visibily and clearly separate workspace is needed. I imagine if I take up working remotely or for myself again seriously I'll setup a space either in the garage or buy a small shed and setup shop in there. Ride my bicycle around the block to give myself a short 'commute' to seal the deal mentally. 2) Pay for software or people to help you with the books, tracking time and receipts. It will save time, money, and sanity.

* * *

### XML as a datastore
I ran into a situation where the data I needed to store was in a hierarchical structure (well that's what it 'looked' like). Some would be near the root, some would be far down the branches. So out with RDBMS right? XML to the rescue! ... Yeah, that was okay at the beginning but then it grew and really didn't stop. Later on after I left I heard that someone else had to step in and rip that all out so the application would at least startup.

##### What I Learned
* XML is not a data store. It's better as a transmission format. You _could_ use it for small/minor data storage (thus the transformation point). But not for anything big. XML processing is not efficient enough for that.

* If the data doesn't fit well within an RDBMS (and if that's the only option for large data storage) try thinking about the data from a different angle. Perhaps you can put the resulting data in a single record and add all the supposed hierarchical trail in some other form of columns. You just need to play with the data a bit to see what comes out. Keep an open mind.


[PL/B]:http://en.wikipedia.org/wiki/Programming_Language_for_Business
