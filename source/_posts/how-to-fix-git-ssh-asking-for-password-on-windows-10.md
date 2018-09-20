---
title: How to fix git ssh asking for password on Windows 10
date: '2018-09-19T22:39:14-05:00'
tags:
  - git ssh windows10
---
TL;DR
```
Get-Command ssh
```

If the output of that lists an executable **not** in your git usr/bin directory then do this:

```
git config core.sshCommand (get-command ssh).Source.Replace('\','/')
```
Or, if you want to test this in your current PowerShell session w/o messing with Git config
```
$ENV:GIT_SSH_COMMAND = (get-command ssh).Source.Replace('\','/')
```

### Why does this work?
When you install git, it **comes with ssh**. But if you have a newer version of Windows 10, Windows has an install of SSH that comes with it. Installed in `C:\Windows\System32\OpenSSH`. **That** gets put into the environment PATH and so testing:
```
ssh -T git@github.com
```
Uses your key you added via `ssh-add` using the Windows provided binaries. But git is using the ssh stuff within the git usr/bin folder. Different set of keys. So you'd end up getting prompted for your passphrase every single time you git pull.

##### Nearly drove me crazy, this did.
