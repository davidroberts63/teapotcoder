title: 'Fork & Pull: Powerargs'
date: 2014-05-29 00:00:00
tags:
---
I came across the [PowerArgs][] command line parsing .NET library while exploring the latest updated repositories on [Github][] looking for a project to contribute to. Checking its [pulse][] was encouraging and it had a simple enough [issue][exceptions-issue] for me to cut my teeth on for the project.<!--more-->It was not until halfway through my work on that issue that I discovered that the attention grabbing project [ScriptCS][] uses it. Wonderful! I say to myself and forge ahead improving the project the little bit that I can.

Oh, and it has tests. Note to other projects, you have tests? Yes? Good, thank you. That helps me know when I screw up.

You can see by the [execptions issue][exceptions-issue] comments that someone else was trying to get to this item. However it seemed as though time eluded that individual. I do hope that person is able to contribute in the future.

I prefer to give some notice of intent to contribute so as to avoid duplication of work if someone else is already working on it. A few days to give others a chance to chime in sounded like a decent notice and that it was. [Adam][] is nice to work with. Responded in a good amount of time and was appreciative of my [contribution][]. I'm glad he asked what my approach to the issue would be as this is a good sign of project invovlement and forces contributors to think a bit before making serious modifications.

I did make some changes to the tests that are a bit different from the rest of the tests. Any tests that I touched I renamed to describe what was being tested in a bit more detail. Following the 'ThingDoesActionWhenSituation' and 'ThingThrowsWhenSituation' somewhat. Also adusted how some of the assertions were being performed. I see a lot of people (in several other projects) using Assert.IsTrue when an AreEqual can provide better feedback when the test fails. AreEqual will tell you what the actual data that came back is so you can better determine what the problem is.

I did offer to revert the naming and assertion changes I had made. They are my opinonated changes but I will always defer to the owner and main contributors to follow the project style if they request. It's important to balance moving the code in a direction with keeping it consistent across contributors. This helps the readability of the code so you don't find your self context switching just because someone else wrote the code.

I may end up working on another issue [multiple shortcuts][]. [Glenn Block][] himself has requested it. I might just contact him online to see if it's still an issue for him.

Overall, I'm glad that I was able to help. I hope to help some more. Take a look at it yourself and make your command line argument parsing so much easier. You can get it via [Nuget][] as well. I'll be posting a short 10 minute video on it as sort of a show and tell soon so be sure to keep an eye out for that as well.

[contribution]: https://github.com/adamabdelhamed/PowerArgs/pull/19
[multiple shortcuts]: https://github.com/adamabdelhamed/PowerArgs/issues/17
[Glenn Block]: https://twitter.com/gblock
[powerargs]: http://github.com/adamabdelhamed/PowerArgs
[github]: http://github.com
[exceptions-issue]: https://github.com/adamabdelhamed/PowerArgs/issues/11
[pulse]: http://github.com/adamabdelhamed/PowerArgs/pulse
[scriptcs]: http://scriptcs.net/
[adam]: https://github.com/adamabdelhamed
[pull request]: https://github.com/adamabdelhamed/PowerArgs/pull/19
[internal]: http://msdn.microsoft.com/en-us/library/vstudio/7c5ka91b.aspx
[internalsvisibleto]: http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.internalsvisibletoattribute.aspx
[nuget]: http://nuget.org/packages/PowerArgs/