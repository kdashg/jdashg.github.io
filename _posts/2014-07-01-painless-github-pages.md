---
layout: post
category : pages
tags : [github, pages, jekyll]
---

Getting started with GitHub Pages
===

Grab this: <http://jekyllbootstrap.com/>
Follow the instructions there.

Or here:

    git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.io
    cd USERNAME.github.com
    git remote set-url origin git@github.com:USERNAME/USERNAME.github.io.git
    git push origin master

If you already had something at USERNAME.github.io, you'll need to use `push -f`.

Open up `_config.yml`.
Look around and set things up.
In particular, setup:
* title
* tagline
* author
* production_url

Disable the following by setting the provider to false.
Or not, if you want to set these up already.
* comments
* analytics

We also want to opt-in to using GitHub's soon-to-be-default kramdown markdown parser.
Add this to the bottom:
    markdown: kramdown

Here Be Dragons
---

I'm not done yet. This is part of the test.
