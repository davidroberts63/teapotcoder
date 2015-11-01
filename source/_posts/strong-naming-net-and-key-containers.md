title: Strong Naming .NET and Key Containers
date: 2014-06-03 00:00:00
tags:
---
There's a lot of documentation about strong naming .NET assemblies. Most talk about using an open strong name file (snk). Some talk about the password protected file (pfx). Even fewer mention the key container.<!--more--> I'll just get right to the situation and the point.

I'm strong naming assemblies primarily so they can be added to the GAC. Can't let the full pfx sit on the developers' laptops for security concerns. Delay signing just seems like it's going to cause more troubles than it is worth. Since we are using a central build server I'm just telling msbuild to sign the assemblies on the fly. Yeah for automation!

When you do that by specifying the `AssemblyOriginatorKeyFile` msbuild property and that file is password protected (a pfx file) then msbuild will complain about the password. It will also tell you that you can import it into a key container with a name that msbuild has specified. So I do that. Well, not me really but the security team. They are the ones with the password anyway.

```
C:\Program Files (x86)\Microsoft SDKs\Windows\v8.0A\bin\NETFX 4.0 Tools\sn.exe -i [PATH_TO_YOUR_PFX_FILE] [KEY_FROM_MSBUILD_ERROR_OUTPUT]

```

The import is tied to the contents of the file. Not the file name or the path.

Now from my thinking the key is inside that key container; so I don't need to use the `AssemblyOriginatorKeyFile` property anymore right? To back that up there is another msbuild property called `AssemblyKeyContainerName`, heck there's even one called `AssemblyKeyName` although that's in an extension pack. So out with `AssemblyOriginatorKeyFile` and in with `AssemblyKeyContainerName`.

**Nope.**

Doesn't work. No one's going to tell you directly either.

I assumed something. We all know what that means.

If you do import the password protected pfx into that key container then you still must specify that `AssemblyOriginatorKeyFile` pointing to the pfx file. MSBuild will look in its container for the private key data corresponding to that file you specified and the container it told you earlier.

Oh and the `AssemblyKeyContainerName` msbuild property does absolutely nothing as far as I can tell. I wish it did because the container name that msbuild wants (VS_KEY_RANDOMALPHANUM) is nice and ugly.
