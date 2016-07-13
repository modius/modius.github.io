---
layout: post
title: "Lucee session clustering with memcached storage in a Docker container"
description: "."
modified: 2016-07-13
tags: [lucee, memcached]
---

Adding support for **memcached** session storage to a container requires changes to both the project configuration and the nominated environment variables.

# Lucee 4.5

## Container Changes

Add the following items to your project Dockerfile.  These are non-volatile changes so add the following lines near the top of your Dockerfile beneath the `MAINTAINER`:

```bash
# Files for memcached extension support
ENV LUCEE_SESSION_STORE "memory"
ENV LUCEE_APPLICATION_SESSIONCLUSTER "false"
ADD https://s3-ap-southeast-2.amazonaws.com/daemon-provisioning-resources/lucee/4.5/memcached/setenv.sh /u/local/tomcat/bin/
ADD https://s3-ap-southeast-2.amazonaws.com/daemon-provisioning-resources/lucee/4.5/memcached/1.0.0.21.rep /opt/lucee/server/lucee-server/context/extensions/22E5066D7B123C5D4898C712C0438CFA/
ADD https://s3-ap-southeast-2.amazonaws.com/daemon-provisioning-resources/lucee/4.5/memcached/MemCached.cfc /opt/lucee/server/lucee-server/context/context/web-context-deployment/admin/cdriver/
ADD https://s3-ap-southeast-2.amazonaws.com/daemon-provisioning-resources/lucee/4.5/memcached/MemCached.cfc /opt/lucee/web/context/
ADD https://s3-ap-southeast-2.amazonaws.com/daemon-provisioning-resources/lucee/4.5/memcached/commons-pool-1-5-6.jar /opt/lucee/server/lucee-server/context/lib/
ADD https://s3-ap-southeast-2.amazonaws.com/daemon-provisioning-resources/lucee/4.5/memcached/lucee-extension-memcached.jar /opt/lucee/server/lucee-server/context/lib/
ADD https://s3-ap-southeast-2.amazonaws.com/daemon-provisioning-resources/lucee/4.5/memcached/memcached-3-0-2.jar /opt/lucee/server/lucee-server/context/lib/
```

**`setenv.sh` changes**

Note the changes overwrite the default Tomcat `setenv.sh` script. If your container already has a custom `setenv.sh` file, you can add these lines to your script instead:

```bash
# substitute memcached variables into lucee-web xml config
sed --in-place -e "s/{env:LUCEE_SESSION_STORE}/${LUCEE_SESSION_STORE}/" -e "s/{env:LUCEE_SESSION_MEMCACHED_SERVERS}/${LUCEE_SESSION_MEMCACHED_SERVERS}/" /opt/lucee/web/lucee-web.xml.cfm
```

**`lucee-server.xml` changes**

If your Dockerfile doesn't already add a custom `lucee-server.xml` file, you will need to do so. This [`lucee-server.xml` example](https://s3-ap-southeast-2.amazonaws.com/daemon-provisioning-resources/lucee/4.5/memcached/lucee-server.xml) works for 4.5, and contains the configuration changes you need for memcached support. If you're going to use this template, download it and make the file part of your project build repo.

But if you already have a project level `lucee-server.xml`, you need to add the following code to the `<extensions>...</extensions>` block:

```xml
<!-- memcached extension; clustered session management -->
<extension 
  author="Michael Offner" 
  category="Cache" 
  codename="memcached" 
  config="{'steps':[],'mixed':{}}" 
  created="{ts '2015-03-06 01:55:09'}" 
  description="Free and open source, high-performance, distributed memory object caching system, generic in nature, but intended for use in speeding up dynamic web applications by alleviating database load." 
  documentation="" 
  forum="" 
  id="16FF9B13-C595-4FA7-B87DED467B7E61A0" 
  image="http://extension.lucee.org/ext/memcached/logo.png" 
  label="Memcached driver (BETA)" 
  mailinglist="" 
  name="memcached" 
  network="" 
  provider="http://extension.lucee.org/ExtensionProvider.cfc" 
  support="" 
  type="server" 
  version="1.0.0.21" 
  video="" />
```

**`lucee-web.xml.cfm` changes**

There are two changes for the `lucee-web.xml.cfm` file; adding the cache store and updating the scope. 

Add the following code to the `<cache>...</cache>` block:

```xml
<connection 
  class="org.lucee.extension.io.cache.memcache.MemCacheRaw" 
  custom="initial_connections=1&amp;socket_timeout=30&amp;alive_check=true&amp;buffer_size=1&amp;max_spare_connections=32&amp;socket_connect_to=3&amp;min_spare_connections=1&amp;failback=true&amp;maint_thread_sleep=5&amp;max_idle_time=600&amp;nagle_alg=true&amp;max_busy_time=30&amp;failover=true&amp;servers={env:LUCEE_SESSION_MEMCACHED_SERVERS}" 
  name="sessions"
  read-only="false" 
  storage="true" />
```

_Note, we're creating a reserved cache store name called `sessions` and we'll look for this specifically when setting up a memcached sessions store._

Update the `<scope />` tag to include these `session-type`, `sessionmanagement` and `session-storage` attributes:

```xml
<scope 
  client-directory="{lucee-web}/client-scope/" 
  client-directory-max-size="100mb" 
  session-type="cfml" 
  sessionmanagement="true" 
  sessionstorage="{env:LUCEE_SESSION_STORE}"/>
```

**`COPY` configs in Dockerfile**
Lucee XML config changes should be stored in the project environment repo and referenced in the `Dockerfile` like so:

```bash
# Lucee server configs
COPY config/lucee/lucee-server.xml /opt/lucee/server/lucee-server/context/lucee-server.xml
COPY config/lucee/lucee-web.xml.cfm /opt/lucee/web/lucee-web.xml.cfm
```

## App Changes


### FarCry Platform

If you're running FarCry, update your `farcryConstructor.cfm` file. Add a default `sessioncluster` value:

```
<cfset THIS.sessioncluster = false />
```

And inside the check for `bUseEnv` (or instead of the line above if you don't check), add this:

```
<cfset THIS.sessioncluster = system.getEnv("LUCEE_APPLICATION_SESSIONCLUSTER") />
```

### Lesser CFML Apps

For those not running FarCry as a framework, you'll need to update your session cluster setting in the `Application.cfc`

```
<cfset THIS.sessioncluster = system.getEnv("LUCEE_APPLICATION_SESSIONCLUSTER") />
```

## Environment Variables

Your deployment process should set these variables:

<table>
  <tr><td>LUCEE_SESSION_STORE</td><td>The name of the memcached store added earlier, `sessions`. If unset, the container will use `memory` and default to in-memory session storage.</td></tr>
  <tr><td>LUCEE_SESSION_MEMCACHED_SERVERS</td><td>A URL encoded list of memcached hosts. Each line should be a host in the form `host1:port`.</td></tr>
  <tr><td>LUCEE_APPLICATION_SESSIONCLUSTER</td><td>`true` or `false`. If set to true, Lucee will check the session store for updates to the session on every request. If you are running sticky-sessions (and you trust them!) you could set this value to false to reduce network chatter between containers and the session store.</td></tr>
</table>

As an example, you might use these lines in a `docker-compose.yml` file:

```yml
    - "LUCEE_SESSION_STORE=sessions"
    - "LUCEE_SESSION_MEMCACHED_SERVERS=mycache:11211"
    - "LUCEE_APPLICATION_SESSIONCLUSTER=true"
```
if you had a link to a memcached container called `sessions` like this:
```yml
    mycache:
      image: memcached
      expose:
        - "11211"
```

## Test Session Failover

If you can't readily run a cluster of Lucee containers you can simulate a failover by stopping and starting the Lucee service.  You may not be able to do this by simply stopping and starting the container, especialy if you are linking a local memcached store.

You can test a local installation to see if your specific set up is working by:

- logging into the webtop (ie. establishing a session)
- shutting down Tomcat/Lucee and show app is dead
- restart Tomcat/Lucee and show you are still logged in

**List your running containers.**

```
$ docker ps
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                         NAMES
d2673526a6dd        yaffaenvdsp_yaffa-dsp       "supervisord -c /etc/"   7 minutes ago       Up 3 minutes        80/tcp, 443/tcp, 8080/tcp     yaffaenvdsp_yaffa-dsp_1
e46c9aca7487        memcached                   "/entrypoint.sh memca"   16 minutes ago      Up 3 minutes        11211/tcp                     yaffaenvdsp_memcached_1
90edea92c5ef        dockerui/dockerui           "/dockerui"              4 months ago        Up 17 minutes       0.0.0.0:81->9000/tcp          dockerui
6d5c1d760a47        texthtml/docker-vhosts      "forego start -r"        4 months ago        Up 17 minutes       80/tcp, 443/tcp               docker_vhosts
46329e209fcf        daemonite/workbench-proxy   "/app/docker-entrypoi"   4 months ago        Up 17 minutes       0.0.0.0:80->80/tcp, 443/tcp   workbench_proxy
```

**Attach a bash shell to the container.**

```
$ docker exec -ti d2673526a6dd bash
```

**Stop/Start tomcat to test session store**

```
root@d2673526a6dd:/usr/local/tomcat# cd bin
root@d2673526a6dd:/usr/local/tomcat/bin# ./shutdown.sh
root@d2673526a6dd:/usr/local/tomcat/bin# ./startup.sh
Tomcat started.
```

**h/t Daemonite @blair for doing most of the heavy lifting ;)**
