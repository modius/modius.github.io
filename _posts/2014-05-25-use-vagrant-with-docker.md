---
layout: post
title: "Use Vagrant with Docker"
date: 2014-05-25 23:52
tags: vagrant chef berkshelf railo
---

> “Vagrant is not for managing machines, Vagrant is for managing development environments”, Mitchell Hashimoto

Mitchell’s quote comes direct from the comments of an interesting [“Docker vs Vagrant” Stackoverflow question](http://stackoverflow.com/questions/16647069/should-i-use-vagrant-or-docker-io-for-creating-an-isolated-environment). Worth a read if only because a founder from both the **Docker** (Solomon Hykes) and **Vagrant** (Mitchell Hashimoto) projects provided answers.

<!--more-->

[Vagrant 1.6](https://www.vagrantup.com/blog/vagrant-1-6.html) was only recently released (MAY 6 2014) with its official support for Docker in tow. A lot of older Docker tutorials incorrectly position Vagrant as a competitor to Docker.

[Vagrant](https://www.vagrantup.com) is for managing development environments and traditionally does this by provisioning virtual machines. [Docker.io](https://www.docker.io) is another form of virtualisation — stands to reason that Vagrant might be useful.

Vagrant can make Docker easier by:

- provisioning a lean virtual machine for the docker daemon; essential for windows and osx environments
- handling file syncing into containers
- managing network port forwarding
- making `vagrant ssh` handy
- tailing container logs and more

Admittedly, many features are of limited use if you are already running a flavour of linux that can can handle Docker natively. But if you must go through a virtual machine, its a constant pain to be juggling commands/ports/syncs from the host to the docker virtual machine and then on to the containers. 

Vagrant is like rum'n'raisin with lemon sorbet; it complements Docker development.

Plus there’s the convenience of having the same `vagrant up` workflow available and standard across developers using different operating environments. Not to mention *everyday virtual machines* are at your finger tips when Docker itself is not an option for your project.

- [Docker: Getting Started](https://www.docker.io/gettingstarted/)
- [Vagrant Docker Provisioner](http://docs.vagrantup.com/v2/docker/basics.html)
- [Vagrant Docker Provider](http://docs.vagrantup.com/v2/docker/basics.html)
- [Docker Cheat Sheet](https://github.com/wsargent/docker-cheat-sheet)

Enjoy!