---
layout: post
title: "more git goodies"
date: 2015-03-08
comments: true
sharing: true
categories: [git]
---

Becoming a git expert is essential to efficiency and confidence as a software developer. I have evolved from someone who would re-clone the repo in case of a botched merge to a savvy git handler, through:

* Moving files between working directory, index, stash, and commit history
* Working with multiple remotes with many contributors
* Handling changes to forks, branches, and pull requests
* Accidental merges from pulling upstream, and checking out pull requests or remote branches

{% codeblock lang:bash My old favourites %}
git rebase -i <sha> # pick a sha from your git history and start squashing
git clean -f # removes untracked files from your working directory
git stash # save your idea for later
git stash pop # revive your last idea
git reflog # oh my goodness, what did I just do?
{% endcodeblock %}

{% codeblock lang:bash My alii %}
# Add this to your .bashrc (or .zshrc)
alias wip="git add .; git commit -m 'WIP'"  # saves and commits the current state
alias popwip="git reset --soft head~1; git reset head ."  # pops the top commit off the stack

# Add this to your git config
[alias]
  hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short

$ git hist # opens a human readable history -- essential for verifying where you are:
* 2ae2be1 2015-03-08 | WIP (HEAD, origin/source, source) [keighty]
* 247f86f 2015-02-16 | Change portfolio subtitle. [keighty]
* e2a4fd0 2015-02-16 | Add facebook and google+ sharing options. [keighty]
* e41ca0a 2015-02-16 | Replace comments in draft and post tasks. [keighty]
...
{% endcodeblock %}

{% codeblock lang:bash Check out a remote branch %}
git checkout -b <name of the local branch to create> upstream/<name of the remote branch>
{% endcodeblock %}

{% codeblock lang:bash My new favourites %}
git checkout - # checks out the last branch you were on
git checkout -p # checks out changes in chunks
{% endcodeblock %}

I have already shared a few tools that have become so much a part of my tool box, and have developed such a muscle memory that my fingers will perform the tasks as I think them. Every time I pair with another developer I gain another shortcut! Thanks to Vince for those last two...
