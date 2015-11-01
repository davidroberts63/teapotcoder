title: NancyFX Testing with an authenticated user
date: 2014-05-08 00:00:00
tags:
---
I've been trying [NancyFx][] lately and ran across a bit of a misunderstanding on my part and a minor missing point in the Nancy documentation. I'm testing my modules and some require authentication.<!--more--> By way of:

```csharp
this.RequiresAuthentication();
```

The [Authentication][] documentation for Nancy states that if the `NancyContext.CurrentUser` property is `null` then the current request has not been authenticated otherwise the request has been authenticated. Well, not quite. Nancy checks that `CurrentUser` property for null. It also checks that the `UserName` property is not empty. So be sure that your identity has the `UserName` set which is almost always the case.

But for testing I usually setup the minimum amount of data required to keep the distractions as low as possible. What I also do for testing is create a custom bootstrapper just for use in my tests.

```csharp
public class BootstrapperWithLoggedInUser : MyApplicationCustomBootstrapper
{
    protected override void RequestStartup(TinyIocContainer container, IPipelines pipelines, NancyContext context)
    {
        base.RequestStartup(container, pipelines, context)

        context.CurrentUser = new MyApplicationUserIdentity() { UserName = "Douglas Adams" };
    }
}
```
That bootstrapper sets up every request with a logged in user. So I don't have to do it on every request in my tests on the `BrowserContext.FormsAuth`. What I initially did in the bootstrapper was not specify a `UserName` for the current user. Everything kept getting redirected to the login page. Once I set the username everything started working correctly in my tests.

For those that may be interested the code in Nancy that does the actual checking for an authenticated user is the [UserIdentityExtensions.cs][] file in Nancy.Security.

[NancyFx]:http://nancyfx.org
[Authentication]:https://github.com/NancyFx/Nancy/wiki/Authentication-overview#what-did-you-say-your-name-was-again
[UserIdentityExtensions.cs]: https://github.com/NancyFx/Nancy/blob/master/src/Nancy/Security/UserIdentityExtensions.cs#L19-21
