title: Developing behind a proxy
date: 2015-03-03 00:00:00
tags:
---
Working behind a proxy can be annoying at times but it's not difficult to work with, most of the time that is. This is mostly here for my own reference in the future and you get the added benefit.
<!-- more -->
##GIT
You need to set two settings to allow git to use your proxy:

```
git config --global http.proxy "http://[username]:[password]@[address/url]:[port]"
git config --global https.proxy "http://[username]:[password]@[address/url]:[port]"
```

Note both of the above settings. http.proxy and https.proxy.

You may also need to turn off secure certificate verification. Why? Some places use a self signed certificates on the proxy for use with internal domain names.

```
git config --global http.sslVerify false
```

##NPM
More proxy settings:
```
npm config set proxy "http://[username]:[password]@[address/url]:[port]"
npm config set https-proxy "http://[username]:[password]@[address/url]:[port]"
```
Turning off secure certificate verification.

```
npm config set strict-ssl false
```

Thank you to the following for their posts providing the above information:

[http://jjasonclark.com/how-to-setup-node-behind-web-proxy]()
[http://stackoverflow.com/questions/783811/getting-git-to-work-with-a-proxy-server]()
[http://stackoverflow.com/questions/12537763/git-ssl-without-env-git-ssl-no-verify-true]()
