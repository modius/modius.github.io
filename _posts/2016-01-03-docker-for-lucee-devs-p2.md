---
layout: post
title: "Docker for Lucee Developers: Part 2"
description: "TLDR; break down the project template and make it your own."
modified: 2016-01-03
tags: [docker, lucee]
---

_TLDR; break down the project template and make it your own._

> Follows on from [Docker for Lucee Developers: Part 1]({% post_url 2016-01-02-docker-for-lucee-devs-p1 %})

Continuous delivery is hard; development pipelines are initimate affairs, tied very closely to the peculiarities of the application. One of the most important aspects of Dockerising development at Daemon was standardising how development pipelines work. We work with a lot of different bespoke applications, and having a standard structure for version control, development and deployment has become a major bonus of moving to Docker.

Our project template or "environment" has a few key requirements: 

- format should work for any language (well at least the ones we work with). For example, we're working with lucee, python and php so far.
- each Docker image has its own project; we name them `CLIENT-env-PROJECTNAME`. For example, `dae-env-prime` for the Daemon public web site (codenamed Prime). 
- the environment allows for the composition of multiple containers, but is designed for working on a specific application. For example, we run both mysql and memcached along on Daemon Prime.
- it is essential that the Docker image can be built locally, and also built remotely as part of a deployment process. For example, Daemon Prime is automatically built on each commit via <http://quay.io> and then deployed to a cluster of nodes at <http://tutum.co>.

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

For Lucee development we use the official [lucee/lucee4-nginx](https://hub.docker.com/r/lucee/lucee4-nginx/) Docker image. It's a "batteries not included" style of image and we need to add our application.

```ruby
FROM lucee/lucee4-nginx:latest

# NGINX configs
COPY config/nginx/ /etc/nginx/

# Lucee server PRODUCTION configs
COPY config/lucee/lucee-web.xml.cfm /opt/lucee/web/lucee-web.xml.cfm

# Deploy codebase to container
COPY code /var/www
```

This simple `Dockerfile` will work for most Lucee apps unchanged.  It copies a specific config for the NGINX, a config for Lucee and your application code under the NGINX webroot.  I'll go into constructing a more specialised Lucee Dockerfile in a later post.

_Note, anything you want built into the Docker image needs to sit beneath the Dockerfile in the directory tree. This is one of the constraints of the Docker build process, and influences the directory structure of the project._

### Vagrantfile

The `Vagrantfile` manages the the Docker host, specifies how the Docker image should be built and the configuration of the container when its run.

_Note, the `Vagrantfile` works best in the [Daemon Docker Workbench](https://github.com/Daemonite/workbench); its only a Vagrantfile in the parent directory so there's no reason not to use it._

```ruby
  ##################################################
  # Launch dev containers
  # - vagrant up lucee
  ##################################################
  config.vm.define "lucee", autostart: true do |lucee|
    lucee.vm.provider "docker" do |docker|
      docker.name = PROJECT_ENV
      docker.build_dir = "."
      docker.env = {
        VIRTUAL_HOST: PROJECT_ENV + ".*, lucee.*"
      }
      # local development code, lucee config & logs
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
    puts '############################################################'
    puts '# ' + PROJECT_ENV.upcase
    puts '#  - hosted at: http://' + PROJECT_ENV + '.dev'
    puts '############################################################'
  end
  ```

A few notes about the Docker provider:

- the container is called `PROJECT_ENV`; that is, the directory name at the root of the project, for example, `lucee-docker-workbench`.
- `VIRTUAL_HOST` is picked up by the reverse proxy built into the Docker host VM; this is awesome. You can add other environment variables here as needed.
- the Docker volumes map the code base into the web root of NGINX, link the Lucee XML config, and pick up various logs for debugging


### ./code 

`./code` is a directory stub that contains all of your application's code. By default its copied directly into the web root of the on board NGINX server.  

In the template this is populated with a **git submodule**. It's not uncommon for us to bring in a range of libraries and the app code base with a list of submodules.  Using submodules gives granular control over the version of each library being built into the image.

_Note, it's a good idea to get into the habit of using SSH keys for your Git Repos.  When you get to the point of automating Docker image builds from private repos it will be a requirement._

### ./config

`./config` is a directory stub for project configuration files. Use a sub-directory for each service.

`./config/lucee` contains the Lucee xml config for the web context; it could contain other configuration files as needed. The official Lucee Docker image is designed for a single web context per container. By default there is a Docker volume in the development setup that maps the `lucee-web.xml.cfm` in your code base to the one in the running container; changes you make in the Lucee admin will be reflected in your project's config file and can be committed to git as needed. 

`./config/nginx` has a base NGINX server config (`nginx.conf`) and a web app specific config (`default.conf`). For a standard Lucee app these could be left unchanged, but I include it because everyone loves to tinker with their web server set up (or is that just me?).

### ./logs

Various log files are mapped out to this location. The project template has `.gitignore` files to keep the directory structure but block commits of the logs.

## Hack Your Own Lucee Project

Download a ZIP of the `lucee-docker-workbench`; it's easier than forking or cloning the original repo. Create a new directory under your Workbench and unzip the contents.  See if you can't get your own Lucee project running.

Hit me up in the comments below with questions.

Next I'll delve into the structure of the official Lucee Dockerfiles, the thinking behind their construction, and tips for how and why you might build your own. 



