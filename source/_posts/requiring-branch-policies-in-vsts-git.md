---
title: Requiring branch policies in VSTS git
date: 2018-02-04T21:08:51.676Z
tags: vsts git corporate
---
At work we've begun moving toward VSTS git. One of the things we require is code review and that is done via pull requests in various git tools (Github, Bitbucket, VSTS, etc). In VSTS you can apply branch policies to any branch in a git repository. One of those policies is to require a minimum number of reviewers, which is done via pull requests.

At this time I'm not aware of a way to setup a template git repository in VSTS that would have that policy. We want the developers to have the freedom to create a repository whenever they want as well as edit the branch policies, but still ensure that the minimum number of reviewers policy is always in place. What we've done for the time being is setup a Jenkins job that goes through each VSTS git repository and ensures the appropriate policy is in place.

\~ Mention Donovan Brown's VSTeam PS Module \~

\~ Show minimal code for processing each repo \~

\~ Show minimal code for building a policy \~

\~ Mention what would be preferred in the future and negatives of current approach \~
