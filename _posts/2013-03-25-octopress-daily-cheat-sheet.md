---
layout: post
title: "octopress blog daily cheat sheet"
date: 2013-03-25 16:14
comments: true
categories: octopress
excerpt_separator: <!--more-->
---

Assumes you have a fully functional Octopress blogging environment. This cheat sheet is a one page reminder of how to generate a blog post, and the markup for the default plugins.

## Working with Blog Posts

**Create a new blog post**

```bash
$ rake new_post["Zombie Ninjas Attack: A survivor's retrospective"]
# Creates source/_posts/2011-07-03-zombie-ninjas-attack-a-survivors-retrospective.markdown
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

<!--more-->
**Other options**

```bash
$ rake watch      # Watches source/ and sass/ for changes and regenerates
$ rake preview    # Watches, and mounts a webserver at http://localhost:4000
```

### Excerpt

Create a teaser using ```<!-- more -->``` where you want the "Read More" button.

### Working with DRAFT posts

Just don't ```$ rake deploy``` or use ```published: false``` in post header.


## Markup

Generally its [Markdown](http://daringfireball.net/projects/markdown/syntax).


### Code blocks
**Backticks**

``` text 
	``` [language] [title] [url] [link text]
	code snippet
	```
```

Some language options: java, js, cfm, apacheconf, sql. Complete list of [Pygments Lexers](http://pygments.org/docs/lexers/)

**Gist blocks**

``` ruby 
	{ % gist gist_id [filename] %}    # Syntax (no spaces in liquid templates)
	{ % gist 996818 %}                # Example
```

### Image

**Image embed**

``` ruby 
{ % img [class names] /path/to/image [width] [height] [title text [alt text]] %}
{ % img http://placekitten.com/890/280 %}
```


### Quotes

**Formatted block quote**

``` ruby 
{ % blockquote [author[, source]] [link] [source_link_title] %}
Quote string
{ % endblockquote %}
```

**Inline pull quote**

``` ruby 
{ % pullquote %}
Surround your paragraph with the pull quote tags. Then when you come to
the text you want to pull, { " surround it like this "} and that's all there is to it.
{ % endpullquote %}
```


