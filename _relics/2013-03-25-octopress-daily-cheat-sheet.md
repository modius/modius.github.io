---
title: "Octopress blog daily cheat sheet"
date: 2013-03-25T16:14:00
tags: [octopress, ruby]
description: "A one page reminder of how to generate a blog post with Octopress."
---

Assumes you have a fully functional Octopress blogging environment. This cheat sheet is a one page reminder of how to generate a blog post, and the markup for the default plugins.

## Working with Blog Posts

**Create a new blog post**

```bash
$ rake new_post["Zombie Ninjas Attack: A survivor's retrospective"]
```

**Save your source files**

```bash
$ git add .
$ git commit -m 'your message'
$ git push origin source
```

**Publish to live site**

```bash
$ rake generate   # Generates posts and pages into the public directory
$ rake deploy     # Pushes it to your GIT master for publication
```

**Other options**

```bash
$ rake watch      # Watches source/ and sass/ for changes and regenerates
$ rake preview    # Watches, and mounts a webserver at http://localhost:4000
```

### Excerpt

Create a teaser using `<!-- more -->` where you want the "Read More" button.

### Working with DRAFT posts

Just don't `$ rake deploy` or use `published: false` in post header.

## Markup

Generally it's [Markdown](http://daringfireball.net/projects/markdown/syntax).

### Code blocks

````
``` [language] [title] [url] [link text]
code snippet
```
````

### Image embed

```
{% img [class names] /path/to/image [width] [height] [title text [alt text]] %}
```
