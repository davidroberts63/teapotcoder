title: To Build and Deploy
date: 2014-05-07 00:00:00
tags:
---
The end goal of every software development project should be to build it and deploy it. Not every language has a 'build' step but in that case I consider running your tests to be part of the build. If you don't build it you can't deploy it and if you don't deploy it what's the point?<!--more-->Educational projects are the exception here since they may not be intended to go to production.

In light of that I prefer to have an automated process do as much as possible for me. Preferrably not on my own machine. So that I am not interrupted during my regular work flow and I can trust the process is done consistently and timely. You can read most of that in two words "Be lazy".

If you don't have an automated build and an automated deployment process you are really missing out on some great productivity gains. As well as a big boost of confidence in your work. Depending on your business environment you may **need** to have a central automated build and deployment process for legal auditing and regulation requirements.

####Here are a few things you have to deal with when you don't have a central automated build.

* No guarantee the artifacts were produced from the code as in source control <a href='#note1' id='source1'><sup>1</sup></a>. Since you could make changes locally before building and forget to commit those changes.
* Build and test <a href='#note2' id='source2'><sup>2</sup></a> problems take much longer to discover. Since you only build when you feel like it. How many times have you forgotton to add a file properly?
* Where is the latest build? Oh Joe did it and it's on the NAS, or wait Bob did it and I think he put it on the NAS but he was heading home so not sure. Nevermind I'll do it right now for you...awww man build errors great, just great.
* What version of the source code did this build come from? This is very important in defect tracking and audits.
* Lack of consistent build environment. You have one version of the build and test tools. Other team members may have a different version of the build and test tools. Now the build is inconsistent and may break for difficult to track reasons.

Sure it's easy to just build the solution right on your machine, you do it all the time during the development process. But so many things can go wrong because *you* are involved in the process. People cause all sorts of problems because we get easily distracted by just about anything.

#### And if you don't have a central automated deployment.

* No guarantee that what is deployed came from an offical set of build artifacts. Who knows what was put in production with this deployment. Yes even if scripted. Since you are deploying from your local machine you can easily introduce some oddity.
* Modified deployment process problems take longer to discover. You may have added a deployment step while others have changed existing ones. You won't know until all of it comes together.
* Inconsistent deployment environment. Different versions of deployment tools.
* Less to no visibility of recurring deployment problems.
* Ability to audit is pretty much nil. Because of each of the other problems it would be very difficult to prove what actually happened.
* No real way to determine if what is in production differs from what was supposed to be deployed. This could be useful if you belive production has been cracked or infected. The reason why you can't is because there is no definitive source of what went to production.

####When automated build and deployment is probably not needed

If your team is super small say no more than three people then setting up a central automated build and deployment process **might** be more work than it would be worth. Be sure to take into consideration auditing and regulatory compliance. Most applications may not need to think about that but when you do you had better think about it or it will seriously hurt later on.

####So what's the deal?

A consistent, traceable, repeatable build and deployment process can and usually does instill more trust within your organization. This will allow you to deploy much quicker and shorten the feedback cycle so you can know when you are doing right and wrong earlier in the development process. [Rob Sullivan](http://twitter.com/datachomp) calls this "Success feeds Success".

You may not think it is important now but later on when that deadline is looming overhead and you need to get features out faster you would be much better off with an automated build and deployment process. It is one less thing for you to think about since it will just work for you not against you. I'll write a few posts about different ways to have an automated build and deployment process so keep an eye out.

##### Footnotes
1. Not using version control? Different discussion, bigger problem. <a id='note1' href='#source1'>go back</a>
2. No tests? Different discussion, bigger problem. <a id='note2' href='#source2'>go back</a>
0. Thanks to [Jeff French](http://www.twitter.com/jeff_french) and [Alex Papadimoulis](http://www.twitter.com/apapadimoulis) for their talks at [kcdc13](http://kcdc.info) that gave me the last couple of points I needed to finish this post.