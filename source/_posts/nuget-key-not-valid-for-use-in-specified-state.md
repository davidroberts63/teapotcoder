---
title: 'Nuget: Key not valid for use in specified state'
date: '2018-12-13T15:51:42-06:00'
tags:
  - nuget
---
If you have set a username and password for use with a NuGet repository, for example with [Azure DevOps Artifacts](https://azure.microsoft.com/en-us/services/devops/artifacts/), and you get the following error when doing a nuget install/list etc..:
```
Key not vaild for use in specified state.
```
It's talking about the encryption key used to encrypt your password. Meaning, you copied your nuget.config from a different machine. Run the `nuget sources update .....` command **on the same machine** you are using and you should be good to go.

I ran into this when I did `nuget sources update` on my local machine, then copied the nuget.config into a container, expecting it to work. But a container uses a different encryption key than my host.
