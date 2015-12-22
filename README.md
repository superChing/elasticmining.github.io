# ElasticMining Website
----

This is the repository of [ElasticMining](http://www.elasticmining.com) website.

## Ways to Contribute
----
If you want to make some conbribution, you can

1. Fix the typos, missed translations, or suggest in the issues
2. Fix the HTML or CSS errors
3. Write articles


## Basic Git Tutorial
----
If you are not familar to Git, please read [this basic git tutorial](http://www.slideshare.net/pokaichang72/git-42427674).
I highly recommend everyone should read this, because its fun and infomative.

## Standard Workflow
----
1. fork this repository
2. clone the forked repository to your local machine
3. `cd /path/to/repo` and `git branch develop` to create a new branch for further modification
4. `git checkout develop` to switch the branch you just created
5. `git remote add upstream https://github.com/ElasticMining/elasticmining.github.io.git` to add this repository as a remote upstream ropository
6. `git fetch upstream` to update from the upstream repo
7. `git rebase upstream/dev` to merge remote updates to the local repo 
8. The steps above are for initialize your local git repo

Every time before you make any change, repeat step 6 and  7 to sync the dev with newest version on remote repo, and then make branch to modify.


## Add New Posts
-----
Make sure you still in the branch `develop`

1. Copy the template `0000-00-00-template.md` from `_drafts` to `_posts` and rename the file name as your will
2. Edit the post description in the header
3. Finish the markdown article


The following is the notorious git part.
Make sure you are on the `dev` branch.
1. `git add .` to stage all the changes you made
2. `git commit -m "leave some comment"` to coomit the change
3. `git fetch upstream` to sync upstream repo
4. `git rebase upstream/dev` to rebase the remote branch and your dev.
5. `git push origin dev` to update the local develop branch to your forked repo
6. Okay, now you can issue a pull request


### When You Need Do Modification After Issuing a Pull Request
-----

Just do all modification you need, and sync to your forked repo.
Pull Request will update all the changes you made automatically.


## Post Header
-----

A post header is comprised as follows

```
---
layout: post
title: Genesis
categories: template
description: "This is for testing"
comments: true
tags: [love, coding, machine learning]
image:
  feature:
  credit: 
  creditlink: 
date: 0000-01-01T00:00:00+00:00
author: mayyang
---

# This is markdown section
```
The hightlighted header option needs you to customize

1. layout: layout of this post, remain as it is
2. **title**: title of this post
3. **categories**: the category this post should belong
4. **comments**: true or false. Does this post allow visitors to leave comments?
5. **tags**: the tags this post should have
6. **image**: the featured image shown in the head. You can leave it blank
7. **date**: date
8. **author**: the author id you are assigned, which is listed as follows
9. **description**: the abstract of this post

### Author IDs
Please use these IDs for the author info in post header

```
  May Yang: mayyang
  Erica Li: ericali
  Brayn Yang: bryanyang
  Mark Yang: markyang
  Chu-Yu Hsu: chuyuhsu
  Ryan Chao: ryanchao
  Larry Lo: larrylo
  Wei-Ching Lin:  superching
```


## How to Test This Jekyll Site Locally.
-----
First of all, you have to setup the jekyll environment
[Here](http://networm.me/jekyll-note.html) is a step-by-step go-through.

1. Install rvm
2. Install ruby 2.2
3. `gem install jekyll`
4. `gem install jekyll-paginate`
5. `gem install pygments.rb`
6. `gem install kramdown`

Okay, if you still no clue what's going go.
Please refer to [here](http://networm.me/jekyll-note.html)

Now, you already have set the jekyll environment,
You can start a jekyll server to view the change you made.

1. `cd /path/to/repo`
2. `jekyll serve`
3. open your browser and go to "http://127.0.0.1:4000"

