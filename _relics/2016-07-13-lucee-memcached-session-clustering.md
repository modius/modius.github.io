---
title: "Lucee session clustering with memcached in Docker"
date: 2016-07-13
tags: [lucee, memcached, docker]
description: "Adding memcached session storage to a Lucee container for clustered session management."
---

Adding support for **memcached** session storage to a container requires changes to both the project configuration and the nominated environment variables.

## Lucee 4.5

### Container Changes

Add the following items to your project Dockerfile. These are non-volatile changes so add the following lines near the top of your Dockerfile beneath the `MAINTAINER`:

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

If your Dockerfile doesn't already add a custom `lucee-server.xml` file, you will need to do so. If you already have a project level `lucee-server.xml`, you need to add the following code to the `<extensions>...</extensions>` block:

```xml
<!-- memcached extension; clustered session management -->
<extension
  author="Michael Offner"
  category="Cache"
  codename="memcached"
  id="16FF9B13-C595-4FA7-B87DED467B7E61A0"
  label="Memcached driver (BETA)"
  name="memcached"
  provider="http://extension.lucee.org/ExtensionProvider.cfc"
  type="server"
  version="1.0.0.21" />
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

```bash
# Lucee server configs
COPY config/lucee/lucee-server.xml /opt/lucee/server/lucee-server/context/lucee-server.xml
COPY config/lucee/lucee-web.xml.cfm /opt/lucee/web/lucee-web.xml.cfm
```

### App Changes

Update your `Application.cfc` session cluster setting:

```
<cfset THIS.sessioncluster = system.getEnv("LUCEE_APPLICATION_SESSIONCLUSTER") />
```

### Environment Variables

Your deployment process should set these variables:

| Variable | Purpose |
|----------|---------|
| `LUCEE_SESSION_STORE` | Name of the memcached store (`sessions`). Defaults to `memory`. |
| `LUCEE_SESSION_MEMCACHED_SERVERS` | URL encoded list of memcached hosts (`host1:port`). |
| `LUCEE_APPLICATION_SESSIONCLUSTER` | `true` or `false`. If true, Lucee checks the session store on every request. |

Example `docker-compose.yml`:

```yml
    - "LUCEE_SESSION_STORE=sessions"
    - "LUCEE_SESSION_MEMCACHED_SERVERS=mycache:11211"
    - "LUCEE_APPLICATION_SESSIONCLUSTER=true"
```

### Test Session Failover

Simulate a failover by stopping and starting Tomcat inside the container:

```bash
$ docker exec -ti CONTAINER_ID bash
root@container:/usr/local/tomcat/bin# ./shutdown.sh
root@container:/usr/local/tomcat/bin# ./startup.sh
```

Log in before the restart, verify the session persists after.

*h/t Daemonite @blair for doing most of the heavy lifting.*
