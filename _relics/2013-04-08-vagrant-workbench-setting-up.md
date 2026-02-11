---
title: "Vagrant Workbench: Setting up"
date: 2013-04-08
tags: [vagrant, chef, ruby]
description: "Step by step setup guide for the Vagrant development workbench on OSX."
---

The guide setup assumes you're running a relatively modern version of OSX. But if you can get your non-OSX environment into shape, you should be able to follow along with the rest of the guide quite easily.

## Install Homebrew

```bash
$ ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

## Install Git

```bash
$ brew install git
$ git --version
```

## Install rbenv and ruby-build

```bash
$ brew install rbenv
$ if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi
$ brew install ruby-build
```

## Install Ruby

```bash
$ rbenv install 2.0.0-p0
$ rbenv global 2.0.0-p0
$ gem install bundler
```

## Install Berkshelf

```bash
$ gem install berkshelf
```

## Install Foodcritic

```bash
$ gem install foodcritic
```

## Install VirtualBox

Download from the [VirtualBox Downloads Page](https://www.virtualbox.org/wiki/Downloads) and install.

## Install Vagrant

Download the [Vagrant installer](http://downloads.vagrantup.com/) for your operating system.

```bash
$ vagrant plugin install berkshelf-vagrant
```

## Creating the Cookbook

```bash
$ berks cookbook workbench --foodcritic
```

### Starting your virtual machine

Edit the `Vagrantfile` to use Ubuntu:

```ruby
Vagrant::Config.run do |config|
  ...
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  ...
end
```

Then fire it up:

```bash
$ cd workbench
$ bundle install
$ vagrant up
```

If at anytime your virtual machine becomes unstable:

```bash
$ vagrant destroy
$ vagrant up
```

- [Part 1: Getting Started](/relics/vagrant-workbench-getting-started/)
- Part 2: Setting Up *(you are here)*
