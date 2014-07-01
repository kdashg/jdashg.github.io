---
layout: post
tagline: "An exercise in writing my own docs"
category: pages
tags: [github, pages, jekyll]
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

Disable the following by setting the provider to false:
(Or not, if you want to set these up already)

* comments
* analytics

We also want to opt-in to using GitHub's soon-to-be-default kramdown markdown parser.
Add this to the bottom:

    markdown: kramdown


Committing Changes
---

`git add` any new files you made and want to commit.

`git commit -a` to commit the files you added, and any that have been modified.

`git push` to upload the changes.


The Shortest `vi` Tutorial in the World
---

Enter insert mode by typing `i`.
In `i` mode, move around with the arrow keys, and type as if you were using a GUI text editor.
Press `esc` at any time to get back out of any mode.

When you're done, hit `esc`, then type `:x` and hit enter to save your changes.
If you don't want to save your changes, use `:q` instead of `:x`.


Making a Markdown Post
---

Jekyll posts live in `REPO/_posts/`.
Add a new post just by creating a file. (maybe use `vi`!)
The name of the file has to look like YY-MM-DD-TITLE.md.
For instance, this page lives at `REPO/_posts/2014-07-01-painless-github-pages`.

Each Jekyll post needs to start with a 'YAML front-matter' block.
The front-matter block for this post looks like this:

    ---
    layout: post
    tagline: "An exercise in writing my own docs"
    category: pages
    tags: [github, pages, jekyll]
    ---

More info on front-matter is available here: <http://jekyllrb.com/docs/frontmatter/>

After the closing `---` of the front-matter, just start writing markdown.
(Or in our case, technically Kramdown)
Here's the syntax doc for Kramdown: <http://kramdown.gettalong.org/syntax.html>

Once you're done writing something, commit and push the changes to GitHub.
Check http://USERNAME.github.io to see if it's working at all.
GitHub says that it takes about 10 minutes to initialize your Pages repo after your first commit.
It took about 9 minutes for me.
Subsequent commits seem to be reflected by updates to my Pages much more
quickly, sometimes almost immediately after I push.


Deleting Posts
---

Jekyll creates a default post.
Since you should have a post by now, we can delete the default one.
Before you delete it, though, you should go through it and follow its instructions.

When you're ready to delete it, just delete its file: `rm _posts/core-samples/*`.
Alternatively, just nuke its directory: `rm -r _posts/core-samples`.
Don't accidentally `rm -r _posts` though!


Here be Dragons
---

I'm not done yet. This is part of the test.
