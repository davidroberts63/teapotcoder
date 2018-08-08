---
title: Docker volumes and mounts in VSTS agents
date: '2018-08-08T00:11:17-05:00'
tags:
  - docker vsts
---
\- VSTS runs docker commands in command prompt, not PS. So use double quotes, no single quotes.

\- docker --mount should be used in favor of docker -v.

\- When using docker -v use docker inspect, note that the volumes lists one and the mounts list others, but neither match. Indicating a source of the problem.
