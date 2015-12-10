---
layout: post
title: "Setting Up an Existing Octopress Blog"
date: 2013-04-06 13:32
comments: true
categories: octopress
---

Once you have your Octopress blog in place, you might wonder how you start contributing from another computer; either yours or a friends.

You need to setup Octopress for each computer but not from scratch. Clone your repo, switch to the source branch, then manually set up a ```_deploy``` folder and bind it to the master branch.

**Setting Up an Existing Octopress Blog**

``` bash 
$ git clone git@github.com:usr/usr.github.com.git
$ cd usr.github.com
$ git checkout source
$ mkdir _deploy
$ cd _deploy
$ git init
$ git remote add origin git@github.com:usr/usr.github.com.git
$ git pull origin master
```

All done. Courtesy of [code.dblock.org](http://code.dblock.org/octopress-setting-up-a-blog-and-contributing-to-an-existing-one)