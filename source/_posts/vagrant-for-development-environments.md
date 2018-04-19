---
title: Vagrant for development environments
date: 2014-06-08 00:00:00
tags:
---
I have been using [Vagrant][] for my development environments for about six months now. It's a wonderful setup that eases the ability to keep different configurations separate as well as to keep my host machine clean from inadvertent changes.
<!--more-->
When I do the lite development with Ruby or Python I use Vagrant to manage the runtime environment. I continue to actually write the code using my host machine. This way I can use my Sublime and other development tools setup across all the development machines. Then I can quickly change the runtime, testing environment without affecting the writing envirnonment.

You can use the '[config.vm.synced_folder][]' property in the Vagrantfile to keep a host folder synced up as a mounted folder on your guest Linux machine. From that point if you clone your repository into that folder on your host machine You will also have them in your guest machine.

You may also keep your Vagrantfile under version control. Just check it out to a separate folder. I do this so I can use the same vagrant setup for multiple environments when needed.

Vagrant checkout of Ruby Vagrant config (Vagrantfile and puppet/ansible config)

`/Users/johndoe/devenvironments/rubydev`

Ruby project checkout

`/Users/johndoe/development/my-project`

I set the 'config.vm.synced_folder' to the `/Users/johndoe/development` folder. Change to the rubydev folder on my host and `vagrant up`. In the vm I can change to that dir using the name I specified in the vagrantfile. In this case `/development`

`config.vm.synced_folder "/Users/johndoe/development", "/development"`

I continue writing in my host but runtime, web server (via forwarded ports) and testing are in the vm. This does cause me to switch between host and guest when checking test results or dealing with gems. I would like to get growl setup to talk to the host from the guest so that my test results show as I'm writing within my host machine. But that's for a later time.

Note this also works for .NET development in Windows. Same principle just a different OS. I'm currently using Puppet to manage the configuration. Not sure of the road map of Windows support in Ansible yet.

[Vagrant]:http://www.vagrantup.com/
[config.vm.synced_folder]:http://docs.vagrantup.com/v2/synced-folders/basic_usage.html
