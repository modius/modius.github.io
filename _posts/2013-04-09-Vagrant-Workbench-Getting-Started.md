---
layout: post
title: "Vagrant Workbench: Getting Started"
date: 2013-04-08 14:27
comments: true
categories: vagrant chef berkshelf railo
---

I'm in search of the perfect development environment. It's something that runs anywhere (Windows, OSX, Linux), runs a complete production ready stack, is a doddle to set up, can be completely packed into version control and delivered to a friend at a moments notice.

<!--more-->

I want something:

- portable
- mirrors production
- re-created with a couple of commands
- GIT friendly
- always distributable

What follows is a blow by blow guide, built to help the [Daemonites](http://www.daemon.com.au) improve their development process.  Daemon creates and manages large bespoke [CFML](http://www.getrailo.org/) solutions for many different clients. Each development environment is often quite elaborate and getting set up to contribute to a project can take a fair bit of mucking around... until now ;)

While you will be able to use these services on Windows and Linux workstations, all the walkthroughs assume you're working in an OSX environment. Don't let that scare you, things are remarkably similar cross platform.

## The Tools

The workbench is based on having a number of development tools in place:

- [Virtualbox](https://www.virtualbox.org/) for virtual servers
- [Vagrant](http://www.vagrantup.com/) for managing virtual servers
- [Ruby](http://www.ruby-lang.org/en/) for fairy dust (no need to understand any ruby, just need some useful ruby apps)
- [Chef](http://www.opscode.com/chef/) for cooking up infrastructure via code
- [Berkshelf](http://berkshelf.com/) for Chef cookbook and recipe management

Don't race to download these apps right now; a step by step set up guide follows.

## The Stack

For the sake of the guide, I'm going with a completely open source stack, with a sample, database-driven web application, installed and ready to run on Tomcat. With any luck this should touch on enough areas to help you get up and running with any web stack.

- Ubuntu Precise 64 cloud instance
- Apache (web server)
- Tomcat6 (Java servlet container)
- Railo (open source CFML application engine)
- mySQL (database platform)
- FarCry CMS (open source publishing platform for CFML)

If you are into CFML/ColdFusion (and Railo in particular) this should be right up your alley. If you're not, the principles in the guide should have you well on the way to success. And hey, you might just enjoy ColdFusion to boot.

## The Vagrant Workbench Guide

First we get to grips with Vagrant and VirtualBox; this combo provides a myriad of server operating environments from linux to windows. We'll focus on Ubuntu Precise 64bit for this guide.  

Next use Chef for provisioning our virtual machines; coding up the automated installation of our application and all the apps it needs to function properly. This "ops code" is divided up into "Cookbooks" full of "Recipes" for specific application installations. 

The ruby app "Berkshelf" will be handy for retrieving existing cookbooks and resolving their dependencies. And we'll have to roll our own application cookbook to tie it all together.

- Part 1: Getting Started ```<--- YOU ARE HERE```
- [Part 2 Setting Up](vagrant-workbench-setting-up.html)
- [Part 3] Coming on the never never...
- [Part 4] Coming soon...
- [Part 5] Coming soon...
- [Part 6] Coming soon...
- [Part 7] Coming soon...

Many thanks to [Jamie Winsor](https://github.com/reset/) (aka reset) for potent coding skills, his open nature and the awesome [cookbook guide](http://vialstudios.com/guide-authoring-cookbooks.html) that got me started.

