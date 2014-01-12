---
layout: post
title: "Best Practices - Pull Requests"
description: ""
category: git
tags: []
---

Learning how to contribute to open source projects has been really exciting. Adding value to existing projects, getting social in the coding community, and working on production-level code has been a steep but rewarding learning curve. One important thing to learn is the etiquette of creating pull requests. I read a great post about what to include in a [good commit](http://dev.solita.fi/2013/07/04/whats-in-a-good-commit.html), but I also needed to know how to make a good pull request:

###Branch it
Before making any changes to an open source project, open a new branch. This will allow you to isolate your changes into separate pull requests. For example, I add Feature A, push to master, and create pull request 1. The next day, I add Feature B, push to master, and create pull request 2. If Feature A has not already been reviewed and accepted, Feature B additions will now be encorporated into pull request 1. This is bad practice. Keep each contribution in a separate branch. This will also allow you to keep your current branch up to date with the upstream repository, without affecting your previous work.


###One change, one commit
A commit is not just a backup. Like a migration, your commit history should read like a road map of changes you make to get to your current state -- and changes you should undo when things go wrong. If your commits change dozens of lines of code, understanding the changes is going to require a deep dive into the code.

###Pithy commit messages
Make it easy on your code reviewer by making your commits small enough, and your commit messages detailed enough that they can be understood at a glance.

The workflow, in general:

<ol>
  <li>fork the repo</li>
  <li><code>git clone</code> to local machine</li>
  <li><code>git checkout -b featureA</code></li>
  <li><code>git pull upstream master</code></li>
  <li>make your changes</li>
  <li><code>git commit -am "adds one addition to feature A"</code></li>
  <li><code>git push origin featureA</code></li>
  <li>make pull request</li>
</ol>

Awesome