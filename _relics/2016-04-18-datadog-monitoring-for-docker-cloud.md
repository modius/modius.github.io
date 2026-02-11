---
title: "Datadog monitoring for Docker Cloud"
date: 2016-04-18
tags: [docker, monitoring]
description: "A Datadog stackfile for Docker Cloud to set up basic node and service monitoring."
---

Tutum used to offer a cute set of monitoring graphs on node instances directly within their web dashboard. The acquisition by Docker saw these options vanish with the release of Docker Cloud. That left me searching for a convenient (and inexpensive) way of monitoring Docker Cloud nodes; utilisation, memory-consumption, file-system and so on.

**Enter Datadog.**

![Datadog Dashboard](/assets/images/datadog-monitoring.jpg)

You'll need to set up a trial account at Datadog to get your API key. The service is free indefinitely for less than 5 nodes.

You can add a utility stack to your Docker Cloud set up that automatically deploys the monitoring agent as a container on every node:

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

*Note the need to use `$DOCKERCLOUD_NODE_HOSTNAME` as the hostname. Unfortunately this now gives you an 'orrible UUID as a node name.*
