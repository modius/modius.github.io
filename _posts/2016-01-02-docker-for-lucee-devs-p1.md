---
layout: post
title: "Docker for Lucee Developers: Part 1"
description: "TLDR; install everything. Expect to download about a GIG. Coffee may  be needed. Test a working dev environment."
modified: 2016-01-02
tags: [docker, lucee]
---

_TLDR; install everything. Expect to download about a GIG. Coffee may  be needed. Test a working dev environment._

This tutorial assumes little or no Docker experience, an agnostic development environment, and knowledge of Lucee development.

Docker can be tough to get into; there are a lot of small moving parts that make up a basic development ecosystem, and a whole new vocabulary to pick up.  Nothing is overly complex on its own, however, there are many different ways to approach Docker development and this makes Googling solutions difficult.

Let's get a basic development environment up and running, and hook that up to a basic deployment pipeline. Once you get a handle on things you can decide whether or not you like my approach and start forging a more personal Docker toolkit.

## Docker Basics

Docker needs to run in a supported linux environment; both OSX and Windows require a lightweight Virtual Machine as neither operating system supports Docker natively.  This tutorial will also work with Linux environments, but will ignore the local Docker machine if you have one.

The Docker machine is a quasi-virtualisation environment that runs your application in its own isolated process.  Ok. So its a fair bit cleverer than that, but you can read all about the mechanics of Docker elsewhere.

**Docker image**; we build an image to run as a container.  An image is like a sealed appliance; everything wrapped up in a read-only snapshot, and stored in a Docker repository. When you are happy with your app you can commit it to the repository for use elsewhere.

**Docker registry**; the registry contains a bunch of Docker repositories; a bit like git repositories.  The default registry is Dockerhub by Docker themselves, but there are other commercial registries or you can run your own.  We commit polished images to the repo for use elsewhere.

**Docker container**; a running instance of a specific Docker image.  Once a container is running you can modify files in real time, but when the container stops those changes are lost.  We can run a local image or one pulled from a registry.

## Daemon Workbench

Docker has recently released the [Docker Toolbox](https://www.docker.com/docker-toolbox) to help get development environments up.  We still prefer to run our own environment and this tutorial is based on that approach.  As Docker's native tools improve, we will adjust to use more of their generic offering; docker machine, docker compose, etc.

We use Vagrant to build and provision a VM running Docker. And we use the Docker provider in Vagrant to build and run containers.  Docker native tools can accomplish the same thing, but so far we've found this approach simpler to get people new to Docker up and running.  You need to understand less to get going, plus we automatically provision some nice additional features like hostnames and stats.

Quick install guide for our Docker "workbench" for development; full details about the [Daemon Workbench are available on Github](https://github.com/Daemonite/workbench).

1. **Install Git client**
2. **Install Virtual Box.** [Get the latest and install](https://www.virtualbox.org/wiki/Downloads).
3. **Install Vagrant.** [Get the latest and install](https://www.vagrantup.com/downloads.html). 
4. **Install Vagrant Hostmanager plugin.**  
	`$ vagrant plugin install vagrant-hostmanager`
5. create a local projects directory (can be called anything); for example, `$ mkdir ~/Workbench`
6. copy [Workbench VM Vagrantfile](https://github.com/Daemonite/workbench/blob/master/Vagrantfile) into `~/Workbench` directory

Check the workbench and make sure everything is running properly. This may take a little while depending on your Internet connection.

```bash
cd /Workbench
vagrant up
open http://workbench.dev:81
```

**Troubleshooting**; if you get an error starting up the VM try `vagrant provision`.  Sometimes Vagrant doesn't register the Docker provider quickly enough and assumes Docker is not installed; typically on slower machines bringing up the VM for the first time. The `vagrant provision` command will re-set the Docker environment and can be used at any time.

![DockerUI](/images/posts/wb-dockerui.jpg)

DockerUI is installed by default, and registered to port 81. This utility provides a convenient web GUI to your Docker environment.

## Sample Lucee Docker Project

`lucee-docker-workbench` is a sample project that can be used as a template for any Lucee based docker project (or most other languages for that matter).  Lets get it operational first before we look at breaking down the parts in the second part of the tutorial.

Clone the [Lucee sample project](https://github.com/modius/lucee-docker-workbench), and its underlying submodule:

```bash
cd /Workbench
git clone --recursive https://github.com/modius/lucee-docker-workbench
cd lucee-docker-workbench
vagrant up lucee
```

`vagrant up lucee` will build a local Docker image of the Lucee project and run a Docker container within the parent Workbench VM. 

The Workbench has a reverse proxy in place to automatically set up virtual hosts for your Docker projects via environment variables.  You can see the registered virtual hosts at http://workbench.dev

![Lucee Hello World](/images/posts/lucee-demo.jpg)

The lucee project is available at <http://lucee.dev> by default.  You will need a HOSTS entry to point `lucee.dev` to `172.22.22.22` (the IP of the Docker host).  If you are lucky (and lazy ;) you may be able to use: [http://lucee.172.22.22.22.xip.io/](http://lucee.172.22.22.22.xip.io/)

Test the admin at: <http://lucee.dev/lucee/admin/server.cfm>

The project is configured with a Docker volume that maps that code base into the container, so lets test that by making some simple changes to the `./code/index.cfm` or adding a new template; whatever works for you. Check that you can see those changes reflected in the web browser when you reload.

With any luck you should have a fully functional test project.  Next step is to copy the template, examine the structure and get a real project up and running.

> Follows on to [Docker for Lucee Developers: Part 2]({% post_url 2016-01-03-docker-for-lucee-devs-p2 %})
