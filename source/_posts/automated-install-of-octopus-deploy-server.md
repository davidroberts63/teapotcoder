---
title: Automated Install of Octopus Deploy Server
date: 2014-05-13 00:00:00
tags:
---
I have been using Octopus Deploy for a while at work and now upgrading to their 2.0 line. It's a wonderful product and you should check it out if you haven't yet. One of the things I always want to do is automate the install of application infrastructure items (such as this) so that I can burn and build the servers. This allows me to be better prepared for server outages.
<!--more-->
One of the great things about Octopus Deploy 2.X is that at the end of the server configuration wizard you have the option to get the script to reproduce the configuration.

![](/images/OctopusDeployConfigureScript.png)

The only missing part is the actual installation of the Octopus Deploy server files. The running of the msi. It's simple enough to automate as most msi installers are:

{% gist 3e0a05579e883476fac6 Install-OctopusServer.ps1 %}

Notice that I take the given script and use it within Powershell. Using the Start-Process with the -Wait and -Verb RunAs keeps the commands running in sequence and as the running account. Powershell needs to be running as an administrator in this case or it won't install.

Then run your script that you got from the Octopus Setup Wizard. Here's my complete script:

{% gist 3e0a05579e883476fac6 InstallConfigure-OctopusDeployServer.ps1 %}

The only thing I have not figured out is how to set the RavenDb backup location via this wizard or command line configuration. You can do it via the website api (though I have not verified it myself yet). The only problem is that you won't know what the admin user api key is until you logon to the website. At that time you might as well just set the backup location manually. It is not a problem, just a small change that I know I will always do.

Overall I really like this installation process, especially the providing of the script. It was very simple and fluid. The Octopus Deploy team puts a lot of thought into this and it shows.

If you don't see the embedded gists above just use the links. I'm having a bit of trouble with embedded gists at the moment

Update

I just caught up on my blog reading and the Octopus Deploy team has released the server and tentacle as Chocolatey packages. If your server can access chocolatey.org then it's even easier. Check the octopus blog for details. http://octopusdeploy.com/blog/cinst-octopusdeploy
