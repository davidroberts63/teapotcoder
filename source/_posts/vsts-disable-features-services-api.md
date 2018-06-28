---
title: 'VSTS: Disable features/services API'
date: '2018-06-28T16:01:51-05:00'
tags:
  - vsts api
---
VSTS is rolling out a [new navigation](https://blogs.msdn.microsoft.com/devops/2018/06/19/new-navigation/). One of the features in that is the ability to turn off certain services/features. But you have to go into each project individually. That's manual work, I want to automate it.

So if you want to disable some services/features in VSTS across multiple projects there is a REST API that you can use. It's in preview, and you'll have to script the calls but here you go:

```
PATCH https://{account}.visualstudio.com/_apis/FeatureManagement/FeatureStates/host/project/{project-id}/{feature-id}
content-type: application/json

{"featureId":"{feature-id}","scope":{"settingScope":"project","userScoped":false},"state":0}
```

Replace `account`,`project-id` and `feature-id` as appropriate. Here are the feature id's I know of.

- ms.vss-build.pipelines
- ms.vss-test-web.test
- ms.vss-work.agile
- ms.vss-code.version-control

### Host level settings
You can also do the above at the 'host' level. From my experimentation that's the project collection as a whole.

```
https://{account}.visualstudio.com/_apis/FeatureManagement/FeatureStates/host/{feature-id}
```

Same payload, in that case you are simply ignoring the project. **However**, this doesn't actually remove the service/feature from all the projects. What it does is removes them from the services page. So you won't even see a 'Build and Release' toggle for example. So be careful, if you disable a service at the host level but it's enabled at the project level (or undefined) then the project can still use that service as usual, but you won't be able to turn it on/off via the new navigation ui.

