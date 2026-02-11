---
title: "Use Vagrant with Docker"
date: 2014-05-25
tags: [vagrant, docker]
description: "Vagrant is not for managing machines, it's for managing development environments."
---

> "Vagrant is not for managing machines, Vagrant is for managing development environments" — Mitchell Hashimoto

Mitchell's quote comes direct from the comments of an interesting ["Docker vs Vagrant" Stackoverflow question](http://stackoverflow.com/questions/16647069/should-i-use-vagrant-or-docker-io-for-creating-an-isolated-environment). Worth a read if only because a founder from both the **Docker** (Solomon Hykes) and **Vagrant** (Mitchell Hashimoto) projects provided answers.

[Vagrant 1.6](https://www.vagrantup.com/blog/vagrant-1-6.html) was only recently released (MAY 6 2014) with its official support for Docker in tow. A lot of older Docker tutorials incorrectly position Vagrant as a competitor to Docker.

[Vagrant](https://www.vagrantup.com) is for managing development environments and traditionally does this by provisioning virtual machines. [Docker.io](https://www.docker.io) is another form of virtualisation — stands to reason that Vagrant might be useful.

Vagrant can make Docker easier by:

- provisioning a lean virtual machine for the docker daemon; essential for windows and osx environments
- handling file syncing into containers
- managing network port forwarding
- making `vagrant ssh` handy
- tailing container logs and more

Vagrant is like rum'n'raisin with lemon sorbet; it complements Docker development.

Plus there's the convenience of having the same `vagrant up` workflow available and standard across developers using different operating environments.

- [Docker: Getting Started](https://www.docker.io/gettingstarted/)
- [Vagrant Docker Provisioner](http://docs.vagrantup.com/v2/docker/basics.html)
- [Vagrant Docker Provider](http://docs.vagrantup.com/v2/docker/basics.html)
- [Docker Cheat Sheet](https://github.com/wsargent/docker-cheat-sheet)
