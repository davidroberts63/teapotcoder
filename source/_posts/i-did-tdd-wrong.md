title: I did TDD wrong
date: 2014-10-13 00:00:00
tags:
---
#### My mind still thinks procedurally.

I like TDD, I really do. I'm just doing it wrong. TDD is about DRIVING the design. But many people (and evidently myself) just do it in a 'Test First' way. That really doesn't give you much of an advantage other than a higher code coverage.<!--more-->Which is nice on it's own. However you don't really get the full benefit that TDD is going for. Driving the design by using your tests doesn't say 'You need to write the test first', it requires that to be done by it's definition in order for your test to drive the design. I will give a recent example of my own that illustrates the point.

I needed to create a process that would inform people of updates to things. Let's just say it is updates to RSS feeds you follow. In a procedural way and mentally I imagine for most of us it would go like this.

 * Get the list of RSS feeds to find updates about
 * Go get the updates about those RSS feeds
 * Associate the updates to the people that want to know about them
 * Tell the people about their RSS feed update.

How I did it the wrong way was I wrote some tests first to make sure I could get the updates about the RSS feeds (second step). This was done in a class definition. At first this seems okay, and it was for the most part. Where I failed is the next thing I did. I wrote a test for the first step (list of rss feeds) WITHOUT a test that made me need to do that step. This is the important part. If you don't write a test that causes you to need to do that step stop writing. The test driving the design should fall across something that is missing at some point. It might be a method, a class, a whole library, anything. Many times writing the test will likely not compile (in whatever fashion your language compiles) when you discover the next step.

With TDD you should write a test to accomplish that first goal and drive the design to something effective, simple and maintainable. Nothing more and nothing less. Don't think about the latter steps. Just focus on that one. Then do it again for any requirement to get that first step to complete. Repeat that process for each subsequent step. Also, focus in on one thing. Don't try to stuff everything into one flow. When you get done with step one consider that you may be done with that thing/class/object. You may in fact need to have a separate object for the next step.

As you complete each step of your process you can and should look at how you solved it and potentially refactor the code. Remember refactoring is not intended to change behavior; just how you go about accomplishing the behavior. Your test should change minimally if at all.