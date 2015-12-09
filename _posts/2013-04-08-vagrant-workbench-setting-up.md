---
layout: post
title: "Vagrant Workbench: Setting Up"
date: 2013-04-09 23:52
comments: true
categories: vagrant chef berkshelf railo
---

The guide setup assumes you're running a relatively modern version of OSX. But if you can get your non-OSX environment into shape, you should be able to follow along with the rest of the guide quite easily.

<!--more-->

## Install Homebrew

Homebrew is a package manager for OSX. Follow the instructions on the [Homebrew Installation Page](http://mxcl.github.io/homebrew/) to install Homebrew.

**Install Homebrew**

``` bash 
$ ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```

This guide will assume that you have Homebrew installed.

## Install Git

Git is a distributed version control system that is used heavily by Chef. We need this too.

**Install GIT**

``` bash 
$ brew install git
$ git --version
git version 1.8.2
```

## Install rbenv and ruby-build

OSX comes with its own outdated version of Ruby. Simply upgrading it can cause trouble. You need something to manage multiple versions of Ruby; enter [rbenv](https://github.com/sstephenson/rbenv/) and [ruby-build](https://github.com/sstephenson/ruby-build).

**Install rbenv**

``` bash 
$ brew install rbenv
$ if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi
$ brew install ruby-build
```

EDIT: need to see if the command line entry is good enough or if we should be updating the bash profile.

## Install Ruby

Ruby 1.9 is a requirement of Berkshelf. We will be using 2.0.0-p0 which is the latest patch level of 2.0.0.

**Install Ruby**

``` bash 
$ rbenv install 2.0.0-p0
```

Set Ruby 2.0.0-p0 as your default Ruby version

**Set Global Ruby Version**

``` bash 
$ rbenv global 2.0.0-p0
```

And install bundler for dependency resolution

**Install Ruby Bundler**

``` bash 
$ gem install bundler
```

## Install Berkshelf

[Berkshelf](http://berkshelf.com/) is a dependency resolver and retriever for Chef Cookbooks.

**Install Berkshelf**

``` bash 
$ gem install berkshelf
```

Note: This guide requires Berkshelf (1.0.0) or later.

## Install Foodcritic

[Foodcritic](http://acrmp.github.com/foodcritic/) is a linting tool for Chef Cookbooks that helps you find problems and improve your code.

**Install Foodcritic**

``` bash 
$ gem install foodcritic
```

## Install VirtualBox

[VirtualBox](https://www.virtualbox.org) is a virtualization solution for creating virtual machines on your local computer. We will be using it to build our development environment.

Download Virtualbox from the [Virtualbox Downloads Page](https://www.virtualbox.org/wiki/Downloads) and then install it. We will be using version 4.2.10 in this guide.

## Install Vagrant

[Vagrant](http://www.vagrantup.com/) provides easy to configure, reproducible, and portable work environment built on top of VirtualBox, VMWare, or AWS.

Vagrant can be installed by [downloading the installer](http://downloads.vagrantup.com/) for your operating system and using standard procedures to install that package. We will be using version 1.1.5.

## Install Berkshelf plugin

Install the [Berkshelf plugin for Vagrant](https://github.com/RiotGames/berkshelf-vagrant).

**Install Berkshelf Plugin**

``` bash 
$ vagrant plugin install berkshelf-vagrant
```

## Creating the Cookbook

Create a new cookbook called "workbench" to bake our sample development environment.  Berkshelf will do all the hard work, and set up a standard set of templates. Don't forget the ```--foodcritic``` attribute. This will create a skeleton for a new cookbook named ‘workbench' in the directory ```workbench``` in your current working directory.

**Create A Cookbook**

``` bash 
$ berks cookbook workbench --foodcritic
      create  workbench/files/default
      create  workbench/templates/default
      create  workbench/attributes
      create  workbench/definitions
      create  workbench/libraries
      create  workbench/providers
      create  workbench/recipes
      create  workbench/resources
      create  workbench/recipes/default.rb
      create  workbench/metadata.rb
      create  workbench/LICENSE
      create  workbench/README.md
      create  workbench/Berksfile
      create  workbench/chefignore
      create  workbench/.gitignore
         run  git init from "./workbench"
      create  workbench/Thorfile
      create  workbench/Gemfile
      create  workbench/Vagrantfile
Using workbench (0.1.0) at path: '/Users/modius/Kitchen/workbench'
```

Passing the additional ```--foodcritic``` option generates additional boilerplate files for your cookbook that will enable lint testing with [Foodcritic](http://acrmp.github.io/foodcritic/) later.

### Prepare your virtual environment

Switch into the directory of the newly created cookbook and install the Gem dependencies with bundler

**Install Gem Dependencies**

``` bash 
$ cd workbench
$ bundle install
```

Bundler will install all of the dependent RubyGems and guarantee that you have the right versions.

### Starting your virtual machine

A ```Vagrantfile``` was generated for you by Berkshelf with a boilerplate configuration. The ```Vagrantfile``` defines the configuration of the virtual machine we'll be working with. By default this is configured to download and boot a CentOS 6.3 Vagrant Box and provision it with ```chef-solo```. 

First thing we're going to do is switch this to an Ubuntu virtual instead. Open the ```Vagrantfile``` inside your cookbook with your [favorite editor](http://www.sublimetext.com/2) and edit the values of the ```config.vm.box``` and ```config.vm.box_url``` attributes.

**Edit Vagrantfile**

``` ruby 
Vagrant::Config.run do |config|
  ...
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  ...
end
```

Start up your virtual machine

**Vagrant Up**

``` bash 
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
[default] Importing base box 'precise64'...
[default] Matching MAC address for NAT networking...
[default] Setting the name of the VM...
[default] Clearing any previously set forwarded ports...
[Berkshelf] Updating Vagrant's berkshelf: '/Users/modius/.berkshelf/vagrant/berkshelf-20130409-26494-18obao1'
[Berkshelf] Using workbench (0.1.0) at path: '/Users/modius/Kitchen/workbench'
[default] Fixed port collision for 22 => 2222. Now on port 2203.
[default] Creating shared folders metadata...
[default] Clearing any previously set network interfaces...
[default] Preparing network interfaces based on configuration...
[default] Forwarding ports...
[default] -- 22 => 2203 (adapter 1)
[default] Booting VM...
[default] Waiting for VM to boot. This can take a few minutes.
[default] VM booted and ready for use!
[default] Setting hostname...
[default] Configuring and enabling network interfaces...
[default] Mounting shared folders...
[default] -- /vagrant
[default] -- /tmp/vagrant-chef-1/chef-solo-1/cookbooks
[default] Running provisioner: VagrantPlugins::Chef::Provisioner::ChefSolo...
Generating chef JSON and uploading...
Running chef-solo...
stdin: is not a tty
[2013-04-09T11:45:33+00:00] INFO: *** Chef 10.14.2 ***
[2013-04-09T11:45:34+00:00] INFO: Setting the run_list to ["recipe[workbench::default]"] from JSON
[2013-04-09T11:45:34+00:00] INFO: Run List is [recipe[workbench::default]]
[2013-04-09T11:45:34+00:00] INFO: Run List expands to [workbench::default]
[2013-04-09T11:45:34+00:00] INFO: Starting Chef Run for workbench-berkshelf
[2013-04-09T11:45:34+00:00] INFO: Running start handlers
[2013-04-09T11:45:34+00:00] INFO: Start handlers complete.
[2013-04-09T11:45:34+00:00] INFO: Chef Run complete in 0.027968 seconds
[2013-04-09T11:45:34+00:00] INFO: Running report handlers
[2013-04-09T11:45:34+00:00] INFO: Report handlers complete
```

This may take a little while if this is your first time; Vagrant will need to download the Precise64 base box first. But once the box package is local, any request for a new box of the same name and url will reuse your local copy. If Ubuntu is not your thing, there's a convenient list of pre-prepped and popular [Vagrant boxen](http://www.vagrantbox.es/) online.

Check the full [Vagrant Documentation](http://docs.vagrantup.com/v2) for future reference.

If at anytime your virtual machine becomes unstable or if you'd like to start over you can destroy your virtual machine with one command

**The Green Needle**

```bash 
$ vagrant destroy
Are you sure you want to destroy the 'default' VM? [y/N] y
[default] Forcing shutdown of VM...
[default] Destroying VM and associated drives...
[Berkshelf] Cleaning Vagrant's berkshelf
```

If you destroyed your VM just now becuase you couldn't help yourself, make sure to recreate it with ```vagrant up``` before moving on with the guide ;)

- [Part 1: Getting Started](#)
- Part 2 Setting Up ```<--- YOU ARE HERE```
- [Part 3] Coming on the never never...
