title: The case for abstract IoC
date: 2014-12-15 00:00:00
tags:
---
I really value the IoC libraries out there and the features each one provides. I use one in nearly all the applications I write. Along with IoC libraries there has been the occasional discussion on abstracting it away from the application. I'm not a fan of this idea.
<!--more-->
By abstracting the IoC I mean to abstract out the registration of classes and the resolving of the types during runtime. If I did both then I would lose the features of the library I chose because the abstraction has to go to the lowest common denominator of IoC features. Once I lost those features then I wonder why I picked that library over any other library at all.

Many frameworks ([ASP.NET MVC][], [Nancy][], [FubuMVC][]) do abstract out the IoC. Mostly just the resolving of types but some also do the registration as well. Doing that in the framework is fine because it's intended to be used by many different types of applications. But in the application itself I see no reason to abstract out the registration of classes. The rest of the application should rely on the dependency injection the IoC provides. Any application factories can require an abstracted resolver  since that should just be providing the ability to resolve a type.

If I try to convince myself that abstracting the IoC library would be beneficial I simply remember that I will always have to learn the new IoC library API. I feel the abstraction would give me a false sense of security when switching IoC libraries. If switching IoC libraries turns out to be difficult then I likely used the IoC in the wrong way in my application.

I'll keep the registration of classes in the IoC soley in my application startup, keep the unique features of the IoC library and know what the IoC is doing with my classes.

The case for abstracting IoC libraries doesn't exist for my application, but it can for a framework.

[ASP.NET MVC]:http://www.asp.net/mvc
[Nancy]:http://nancyfx.org
[FubuMVC]:http://mvc.fubu-project.org