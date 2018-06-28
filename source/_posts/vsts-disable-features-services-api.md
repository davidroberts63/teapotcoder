---
title: 'VSTS: Disable features/services API'
date: '2018-06-28T16:01:51-05:00'
tags:
  - vsts api
---
If you want to disable some services/features in VSTS across all your projects there is a REST API that you can use. It's in preview, and you'll have to script the calls but here you go:

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

