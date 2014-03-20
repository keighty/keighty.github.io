---
layout: post
title: "multiple remotes"
date: 2014-03-19
comments: true
categories: [git, I didn't know that]
---

Somehow I had worked out in my head that there could be only one remote. Origin. Turns out you can set and push to a (presumably) infinite number of remote repositories <!--more-->:

{% codeblock Checkout your git config %}
$ git config --list
push.default=nothing
alias.hist=log --pretty=format:"%h %ad | %s%d [%an]" --graph --date=short
user.name=Katie Leonard
…
remote.origin.url=git@github.com:keighty/thorfiles.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master
{% endcodeblock %}

(On a side note, I am not sure I remember putting ``alias.hist`` in my git config, but I find it to be wickedly useful.)

Setting a new remote is as easy as:

{% codeblock 1 2 3 a b c %}
$ git remote add new_destination git@github.com:otheruser/thorfiles.git
{% endcodeblock %}

Standing back to admire the results:
{% codeblock Simple, but does it work? %}
$ git config --list
push.default=nothing
alias.hist=log --pretty=format:"%h %ad | %s%d [%an]" --graph --date=short
user.name=Katie Leonard
…
remote.origin.url=git@github.com:keighty/thorfiles.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master
remote.new_destination.url=git@github.com:otheruser/thorfiles.git
remote.new_destination.fetch=+refs/heads/*:refs/remotes/new_destination/*
{% endcodeblock %}

Fast-forward through some excellent and brilliant feature work on ``my_branch``, and I naturally want to save it for posterity:

{% codeblock Chizeled in metal on GitHub servers %}
$ git push origin my_branch
Counting objects: 611, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (263/263), done.
Writing objects: 100% (375/375), 63.40 KiB | 0 bytes/s, done.
Total 375 (delta 280), reused 189 (delta 105)
To git@github.com:keighty/thorfiles.git
 * [new branch]      my_branch -> my_branch
{% endcodeblock %}

I can also share it with my friend's repo, presuming I have write access, and knowing full well he will want to use my new feature:

{% codeblock Update your friends! %}
$ git push new_destination my_branch
Counting objects: 611, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (263/263), done.
Writing objects: 100% (375/375), 63.40 KiB | 0 bytes/s, done.
Total 375 (delta 280), reused 189 (delta 105)
To git@github.com:otheruser/thorfiles.git
 * [new branch]      my_branch -> my_branch
{% endcodeblock %}

Git is so awesome.