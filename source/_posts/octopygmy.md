---
title: OctoPygmy
date: 2015-02-28 00:00:00
tags:
---
I am a great fan of Octopus Deploy. In the interest of improving it in any way I can I released a beta version of OctoPygmy, a Chrome extension for Octopus Deploy.<!-- more -->Right now it has the following features:


   * Filter the dashboard by project groupings.
   * Filter the machines by environment.
   * Filter the machines by name or role.


Where I work full time we have about 50 projects (never really counted) using Octopus Deploy. For the individual teams the dashboard is fine. They only see what they have access to. But for me and the team I'm on. We see all of them. It gets a bit tedious scrolling every where to find the right project that needs looking into on occasion. The dropdown that OctoPygmy adds makes it much easier to focus on the projects at hand for us.

![](/images/EnvironmentFilterSmall.png)

Similar item for the machines (Tentacles). We have about 70+ machines at the moment being used with Octopus Deploy. There are many more in the company but not for apps. The environment dropdown helps focus on the environment and then the filter by name and role helps even further.

I've read other articles where they have configurations with well over a 100 machines spread across multiple countries. I'm blown away by the scale that others are becoming much more effective and deploying more often in a repeatable way with Octopus Deploy. I hope that this extension finds them and helps them with their larger scale use of Octopus Deploy.

It's open source on github at davidroberts63/OctoPygmy. If you'd like to lend a hand just add an issue and lets talk a bit before you put work into it so we are all on the same page. Right now with this initial version 0.6 the source is not ideal. I want to do a few things before adding some more features, without to much of a delay that is:


   * Add unit tests
   * Get it on a continuous build, maybe Travis-CI or AppVeyor
   * Refactor the code to get DRYer
   * Look into moving away from the mutation events to start using the mutation observer.


Later on I'd like to get a better logo for it. The round circle with eight sided starburst in the middle is what I (non-artist graphic person) did in five minutes using Sketch.io.
