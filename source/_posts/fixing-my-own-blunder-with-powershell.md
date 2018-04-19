---
title: Fixing my own blunder with powershell
date: 2014-06-16 00:00:00
tags:
---
I installed a service a while back that uses Java. When I installed it I followed with a flow of installing Java to a different path from the default (oh no). There are different reasons for that which have now been totally debunked. The service that uses java (Subversion Edge in this case) has a windows service with a path to Java at the time (the non default path).
Then a patch got applied.

It uninstalled the original one and installed the updated one in the default location. So now the windows service is trying to run Java from a location that no longer exists. So how does one fix such a problem given that you can't change the path the windows service is using from the user interface?

**Power**shell

Thanks to [Jeffery Hicks][] and his posts on [Managing Services the Powershell way][] I was able to very easily correct the executable path of that service.

```powershell
$theService = Get-WmiObject win32_service -Filter "name='CSVNConsole'"

$updatedPath = $theService.PathName.Replace('E:\MyOldPathToJava', 'C:\Program Files\Java\jre7')

$theService | Invoke-WmiMethod -Name Change -ArgumentList @($null, $null, $null, $null, $null, $updatedPath) | Select ReturnValue

# Verify the change took.
Get-WmiObject win32_service -Filter "name='CSVNConsole'" | Select *

Start-Service CSVNSonsole
```

When using the `Invoke-WmiMethod` it's important to know what the order of parameters is. What's documented may not be correct. so use `$theService.GetMethodParameters("Change")` to determine the proper order. Ignore those that start with an underscore.

All of this is in very good detail at Jeffery Hicks' blog about this.

On a side note be sure to use `Start-Transcript` when working on stuff like this. It's wonderful to be able to go back and read everything that you did and what the outcome of it was.

[Jeffery Hicks]:http://4sysops.com/jeffery-hicks/
[Managing Services the Powershell way]:http://4sysops.com/archives/managing-services-the-powershell-way-part-6/
