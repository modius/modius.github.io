---
title: "Docker for Lucee developers: Part 2"
date: 2016-01-03
tags: [docker, lucee]
description: "Break down the project template and make it your own."
---

> Follows on from [Docker for Lucee Developers: Part 1](/relics/docker-for-lucee-devs-p1/)

Continuous delivery is hard; development pipelines are intimate affairs, tied very closely to the peculiarities of the application. One of the most important aspects of Dockerising development at Daemon was standardising how development pipelines work. We work with a lot of different bespoke applications, and having a standard structure for version control, development and deployment has become a major bonus of moving to Docker.

Our project template or "environment" has a few key requirements:

- format should work for any language
- each Docker image has its own project; we name them `CLIENT-env-PROJECTNAME`
- the environment allows for the composition of multiple containers, but is designed for working on a specific application
- it is essential that the Docker image can be built locally, and also built remotely as part of a deployment process

The environment is designed to work with the [Daemon Docker Workbench](https://github.com/Daemonite/workbench), but could easily be adapted for use with Docker Machine/Docker Compose.

## Lucee Development Structure

```
lucee-docker-workbench/
├── Dockerfile
├── Vagrantfile
├── code (-> git submodule)
├── config
│   ├── lucee
│   │   └── lucee-web.xml.cfm
│   └── nginx
│       ├── conf.d
│       │   └── default.conf
│       └── nginx.conf
└── logs
    ├── lucee
    ├── nginx
    ├── supervisor
    └── tomcat
```

### Dockerfile

For Lucee development we use the official [lucee/lucee4-nginx](https://hub.docker.com/r/lucee/lucee4-nginx/) Docker image:

```ruby
FROM lucee/lucee4-nginx:latest

# NGINX configs
COPY config/nginx/ /etc/nginx/

# Lucee server PRODUCTION configs
COPY config/lucee/lucee-web.xml.cfm /opt/lucee/web/lucee-web.xml.cfm

# Deploy codebase to container
COPY code /var/www
```

This simple `Dockerfile` will work for most Lucee apps unchanged. It copies a specific config for NGINX, a config for Lucee, and your application code under the NGINX webroot.

### Vagrantfile

The `Vagrantfile` manages the Docker host, specifies how the Docker image should be built and the configuration of the container when its run.

```ruby
config.vm.define "lucee", autostart: true do |lucee|
  lucee.vm.provider "docker" do |docker|
    docker.name = PROJECT_ENV
    docker.build_dir = "."
    docker.env = {
      VIRTUAL_HOST: PROJECT_ENV + ".*, lucee.*"
    }
    docker.volumes = [
      "/vagrant/" + PROJECT_ENV + "/code:/var/www",
      "/vagrant/" + PROJECT_ENV + "/config/lucee/lucee-web.xml.cfm:/opt/lucee/web/lucee-web.xml.cfm",
      "/vagrant/" + PROJECT_ENV + "/logs/lucee:/opt/lucee/web/logs",
      "/vagrant/" + PROJECT_ENV + "/logs/nginx:/var/log/nginx",
      "/vagrant/" + PROJECT_ENV + "/logs/supervisor:/var/log/supervisor",
      "/vagrant/" + PROJECT_ENV + "/logs/tomcat:/usr/local/tomcat/logs"
    ]
    docker.vagrant_machine = WORKBENCH_HOST
    docker.vagrant_vagrantfile = WORKBENCH_VAGRANTFILE
    docker.force_host_vm = true
  end
end
```

A few notes about the Docker provider:

- the container is called `PROJECT_ENV` — the directory name at the root of the project
- `VIRTUAL_HOST` is picked up by the reverse proxy built into the Docker host VM
- the Docker volumes map the code base into the web root of NGINX, link the Lucee XML config, and pick up various logs for debugging

### ./code

`./code` is a directory stub that contains all of your application's code. In the template this is populated with a **git submodule**. Using submodules gives granular control over the version of each library being built into the image.

### ./config

`./config` is a directory stub for project configuration files. Use a sub-directory for each service. By default there is a Docker volume that maps the `lucee-web.xml.cfm` in your code base to the one in the running container; changes you make in the Lucee admin will be reflected in your project's config file.

### ./logs

Various log files are mapped out to this location. The project template has `.gitignore` files to keep the directory structure but block commits of the logs.

## Hack Your Own Lucee Project

Download a ZIP of the `lucee-docker-workbench`; it's easier than forking or cloning the original repo. Create a new directory under your Workbench and unzip the contents. See if you can't get your own Lucee project running.
