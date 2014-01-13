---
layout: post
title: "Git-Picking"
comments: true
description: ""
category: git
tags: []
---

#### or, How to mine for cherries with Git

When I work on an experimental feature in a project branch, I have often found that I will have a mixture of commits I would like to keep, and commits that I wish had never happened.
Using the principle of only committing atomic changes, and enjoying the fruits of your detailed-commit-message labour, there is a way to extract the gold from the ore, so to speak.

##Locate the gold
Checkout the branch that contains your experiment, and use git log to get a list of all the commits:

{% highlight bash %}
$ git checkout form_feature_branch
$ git log --pretty=oneline
a83f4c3aa5d800f846d075748a79a326e0971f67 reorg gemfile
197964c091905a76f2172ca11fcbd49ccfb83c67 adds blog.db
a793fe00c50a5956c5e9f7be48ac5a9861b1eb95 adds forme form to edit...
8394b37494b63754f42ecaf19244fe6d8a36942f adds gitignore for db ...
c52a6fd3f1706584ce3e47abf49ed627a1e28fd3 adds bundler gem for manag...
1686664c4a98221ea04377bcbcf946e62a9f1cfa removes gems for simple...
034d309a612dcce6f10bb55f0da2e78b2f7c10b1 adds cells to project
...{% endhighlight %}

In form_feature_branch I was testing out different implementations of form generation in erb views. I tried a couple of different gems and found one that suits my sinatra project: [Forme](https://github.com/jeremyevans/forme) is easy to use with or without models, and has great sinatra integration and documentation. Also along the way, I discovered how to include bundler for managing file dependencies and did some minor refactoring of my Gemfile.

I wanted to keep the gem reorganization, as well as the forme, bundler, and gitignore commits, so I made this list of keeper-commits:

{% highlight bash %}
a83f4c3aa5d800f846d075748a79a326e0971f67 reorg gemfile
a793fe00c50a5956c5e9f7be48ac5a9861b1eb95 adds forme form to edit...
c52a6fd3f1706584ce3e47abf49ed627a1e28fd3 adds bundler gem for manag...
8394b37494b63754f42ecaf19244fe6d8a36942f adds gitignore for db ...{% endhighlight %}

Everything else is cruft.

##Extract the gold
With this list of golden commits in hand, checkout a fresh branch and cherry-pick the commits you like!
{% highlight bash %}
$ git checkout -b form_feature_additions
$ git cherry-pick <commit_identifier>
{% endhighlight %}

Do this command for each good commit:
{% highlight bash %}
$ git cherry-pick a83f4c3aa5d800f846d075748a79a326e0971f67
[master form_feature_additions] reorg gemfile
 1 file changed, 4 insertions(+), 2 deletions(-)
 changed mode 100644 Gemfile
$ git cherry-pick a793fe00c50a5956c5e9f7be48ac5a9861b1eb95
[master form_feature_additions] adds forme form to edit...
...
$ git cherry-pick c52a6fd3f1706584ce3e47abf49ed627a1e28fd3
[master form_feature_additions] adds bundler gem for manag..
...
$ git cherry-pick 8394b37494b63754f42ecaf19244fe6d8a36942f
[master form_feature_additions] adds gitignore for db ...
...{% endhighlight %}

If you look at your git status you will see

{% highlight bash %}
# On branch form_feature_additions
# Your branch is ahead of 'origin/master' by 4 commits.{% endhighlight %}

##Profit
Now you can merge this branch with master, or continue fleshing out the feature on a clean branch, keeping only the good stuff and abandoning the bad, making experimentation easy.

AWESOME