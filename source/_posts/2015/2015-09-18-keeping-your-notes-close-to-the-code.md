---
layout: post
title: "keep your code close and your notes closer with a global gitignore"
date: 2015-09-18
comments: true
sharing: true
categories: [git]
---

I like to keep my notes as close to the code as possible. When I start on a new code base, I create a folder at the top called `aa_notes`. Super obvious, and it makes an awesome scratch pad for things I don't want to lose. So, how can you do this without checking your folder in to source control? Adding it to a local .gitignore is one strategy, but that change will also need to be checked in to git.
<!--more-->

The answer is to use a global gitgnore.

You can configure a global gitignore file in your home directory that will ignore items in every repo on your machine.

### 1. Add a folder to your source control
{% img /images/150918-keep-your-notes-close/add_note_folder.png %}

### 2. Create a file and git will suggest to track it

```
➜  virtualplaybill2 git:(master) touch aa_notes/this_is_a_note_file.md
➜  virtualplaybill2 git:(master) ✗ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add file..." to include in what will be committed)

        aa_notes/

nothing added to commit but untracked files present (use "git add" to track)
```

### 3. Set up your global gitignore
```
➜  virtualplaybill2 git:(master) ✗ git config --global core.excludesfile ~/.gitignore_global
➜  virtualplaybill2 git:(master) ✗ echo "aa_notes" > ~/.gitignore_global
```

### 4. Git will ignore all folders on your machine named aa_notes
```
➜  virtualplaybill2 git:(master) ✗ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

This comes in super handy when you want to make clarifying notes to yourself without sharing them with all of your collaborators. Of course, if they are good things to share, you should probably add them to the project wiki.

I keep things like daily [infobits](https://www.safaribooksonline.com/blog/2014/06/26/information-flow/), backdoor patches, bits of scripts, and other things. Sometimes writing a note about something helps me remember it a little better, so I still keep my own notes, even if the info is already on the wiki.

Checkout [this post on GitHub](https://help.github.com/articles/ignoring-files/) for this and more ideas for fine tuning what git ignores.
