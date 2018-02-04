---
title: IIS Cannot create file
date: 2016-08-18 00:00:00
tags: iis powershell deploy mvc
---
I just helped a co-worker deploy a ASP.NET MVC app to a new server. But the IIS Powershell cmdlet kept failing saying:

`
Cannot create a file when that file already exists.
`

The eventual source of the problem didn`t fall in line with the error message, big surprise I know.
<!--more-->

## TL;DR
##### Check your app settings for duplicates.

## A bit longer

Part of my deployment process using [Octopus Deploy](https://www.octopus.com) is to set the authentication settings for the web application. Octopus Deploy was helpful in giving quick easy access to the error log. I didn`t need to hunt down and ask why it was failing. Just had to figure out what was actually wrong with my scripting. Using Powershell trying to enable anonymous authentication:

`
Set-WebConfigurationProperty -filter /system.webServer/security/authentication/anonymousAuthentication -name enabled -value "$enableAnonymous" -location $IisItemPath -PSPath "IIS:\"
`

Which may result in the following error:

`
Set-WebConfigurationProperty : Cannot create a file when that file already exists. (Exception from HRESULT: 0x800700B7)
`

I\`m familiar with this error but usually with the **Start-Website** cmdlet. In that case it usually means that you have two sites with the same bindings. That\`s likely due to creating a new site and not removing the default, thus having two sites listening on port 80 or port 443 with same host header. But that wasn\`t the case here.

So what made me think there was something wrong with the `web.config` file? **Experience**. So I just started with something simple to eliminate it as a problem. The `appSettings`, surely that\'s not part of the problem. However, that was exactly the problem. Usually ASP.NET will tell you that you have duplicate configuration items, but in this case it didn\`t for whatever reason. It only gave the error above. It will also give you a similar error if you try to edit the authentication (or other) settings via the IIS GUI.

![](/images/cannot-create-file-iis.png)

Rule of thumb, don\`t trust the error messages to tell you what **your** problem is. It\`ll only tell you what its problem is.

And make sure you don\`t have duplicate app setting entries in your web.config.