---
title: "Vagrant Workbench: Getting started"
date: 2013-04-09
tags: [vagrant, chef]
description: "In search of the perfect development environment. Portable, mirrors production, GIT friendly."
---

I'm in search of the perfect development environment. It's something that runs anywhere (Windows, OSX, Linux), runs a complete production ready stack, is a doddle to set up, can be completely packed into version control and delivered to a friend at a moment's notice.

I want something:

- portable
- mirrors production
- re-created with a couple of commands
- GIT friendly
- always distributable

What follows is a blow by blow guide, built to help the [Daemonites](http://www.daemon.com.au) improve their development process. Daemon creates and manages large bespoke CFML solutions for many different clients. Each development environment is often quite elaborate and getting set up to contribute to a project can take a fair bit of mucking around... until now.

## The Tools

The workbench is based on having a number of development tools in place:

- [Virtualbox](https://www.virtualbox.org/) for virtual servers
- [Vagrant](http://www.vagrantup.com/) for managing virtual servers
- [Ruby](http://www.ruby-lang.org/en/) for fairy dust
- [Chef](http://www.opscode.com/chef/) for cooking up infrastructure via code
- [Berkshelf](http://berkshelf.com/) for Chef cookbook and recipe management

## The Stack

For the sake of the guide, I'm going with a completely open source stack, with a sample, database-driven web application:

- Ubuntu Precise 64 cloud instance
- Apache (web server)
- Tomcat6 (Java servlet container)
- Railo (open source CFML application engine)
- mySQL (database platform)
- FarCry CMS (open source publishing platform for CFML)

## The Vagrant Workbench Guide

First we get to grips with Vagrant and VirtualBox; this combo provides a myriad of server operating environments from linux to windows.

Next use Chef for provisioning our virtual machines; coding up the automated installation of our application and all the apps it needs to function properly.

- Part 1: Getting Started *(you are here)*
- [Part 2: Setting Up](/relics/vagrant-workbench-setting-up/)

Many thanks to [Jamie Winsor](https://github.com/reset/) for potent coding skills and the awesome cookbook guide that got me started.
