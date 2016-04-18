---
layout: post
title: "Datadog Monitoring for DockerCloud"
description: "A Datadog stackfile for dockercloud to set up basic node and service monitoring."
modified: 2016-04-18
tags: [docker, dockercloud]
---


Tutum use to offer a cute set of monitoring graphs on node instances directly within their web dashboard.  The acquisition by Docker saw these options vanish with the release of DockerCloud. That left me searching for a convenient (and inexpensive) way of monitoring Docker Cloud nodes; utilisation, memory-consumption, file-system and so on. 

**Enter Datadog.**

![Datadog Dashboard](/images/posts/datadog-monitoring.jpg)

You'll need to set up a trial account at https://www.datadoghq.com/ to get your API key. The service is free indefinitely for less than 5 nodes.

You can add a utility stack to your Docker Cloud set up that automatically deploys the monitoring agent as a container on every node. Not sure what the original [tutum container](https://github.com/tutumcloud/datadog-agent) offers beyond metadata so here is my stackfile for datadog using their agent container directly:

```yaml
datadog:
  image: 'datadog/docker-dd-agent:latest'
  deployment_strategy: every_node
  environment:
    - API_KEY=YOURAPIKEYHERE
    - HOSTNAME=$DOCKERCLOUD_NODE_HOSTNAME
  privileged: true
  restart: on-failure
  volumes:
    - '/var/run/docker.sock:/var/run/docker.sock'
    - '/proc:/host/proc:ro'
    - '/sys/fs/cgroup:/host/sys/fs/cgroup:ro'
```

_Note the need to use `$DOCKERCLOUD_NODE_HOSTNAME` as the hostname. Unfortunately this now gives you an 'orrible UUID as a node name._

