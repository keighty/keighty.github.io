---
layout: post
title: "git squash to speed up your workflow"
date: 2016-09-12
categories: [git]
---

I am a big fan of work-in-progress (WIP) commits. When I am working through a prototype or a spike, and need to do some experimentation with the code, I use WIP commits like they are save points in a game. <!--more-->These points act like milestones, and allow me to keep working with the confidence that I can always go back to an earlier working state. I use these aliases for the job:

```bash
# in ~/.bashrc
$ alias wip="git commit -am 'WIP'"
$ alias popwip="git reset --soft head~1; git reset head ."
```

When I get the work into a good state, I `wip` it (I may even sing 🎶 [_When a problem comes along... you must whip it!_](https://www.youtube.com/watch?v=IIEVqFB4WUo) 🎶). If I want to grab the last WIP commit from the branch, I `popwip` to pop the last commit off the stack and put the last bunch of changes back into my working directory.

However, when I have been prototyping for a while, making `wip` commits as I go, popping a bunch of commits off the stack gets a little unwieldy. Until now I used an interactive rebase to rewrite my branch's commit history. For example:

```bash
$ git log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short
```
{% img /images/160912-git-squash/githist.png %}

For this git history, I would rebase on sha 83bfc47:

```bash
$ git rebase -i 83bfc47

pick 540c0fe WIP
pick 8af4cfe WIP
pick 50bd7db WIP
pick f90410b WIP

# Rebase 83bfc47..f90410b onto 83bfc47
#
# MOAR git-structions below...
```

I would proceed to `reword` the first and `fixup` the rest, writing a thoughtful and all-encompassing commit message to convey the intention behind this chunk of work. Then I discovered `git merge --squash`

```bash
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

$ git merge --squash test_branch
Updating 83bfc47..f90410b
Fast-forward
Squash commit -- not updating HEAD
 routes/performance.js                          | 3 +++
 routes/performer.js                            | 3 +++
 routes/user.js                                 | 2 ++
 src/app.js                                     | 3 +++
 src/components/performance-frame.jsx           | 3 +++
 src/containers/filterable-performance-grid.jsx | 3 +++
 6 files changed, 17 insertions(+)

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   routes/performance.js
	modified:   routes/performer.js
	modified:   routes/user.js
	modified:   src/app.js
	modified:   src/components/performance-frame.jsx
	modified:   src/containers/filterable-performance-grid.jsx
```

All of the changes I have made on that branch since it diverged from master have been added to the index. I am free to either chunk up the changes into different commits (with commit messages that are more descriptive than WIP), or add them all to a single commit.

GOODBYE `rebase -i`! `Hello merge --squash`!
