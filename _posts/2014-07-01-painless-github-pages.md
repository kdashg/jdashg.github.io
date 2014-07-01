---
layout: post
category : pages
tags : [github, pages, jekyll]
---

Getting started with GitHub Pages
===

It's pretty easy, but I didn't find any one tutorial that was singularly helpful.

Bootstrap Jekyll
---

GitHub Pages automatically run Jekyll when you commit, but the boilerplate to actually use it isn't generated automatically.
Someone made a bootstrap repo though, so we can just start our repo based on that.

Grab this: <http://jekyllbootstrap.com/>
Follow the instructions there.

Or here:

    git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.io
    cd USERNAME.github.com
    git remote set-url origin git@github.com:USERNAME/USERNAME.github.io.git
    git push origin master

If you already had something at USERNAME.github.io, you'll need to use `push -f`.

Setup Jekyll
---

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

Committing changes
---

`git add` any new files you made and want to commit.
`git commit -a` to commit the files you added, and any that have been modified.
`git push` to upload the changes.

The shortest `vi` tutorial in the world
---

Enter insert mode by typing `i`.
Press `esc` at any time to get back out of any mode.
In `i` mode, move around with the arrow keys, and type as if you were using a GUI text editor.
When you're done, hit `esc`, then type `:x` and hit enter to save your changes.
If you don't want to save your changes, use `:q` instead of `:x`

Here Be Dragons
---

I'm not done yet. This is part of the test.
