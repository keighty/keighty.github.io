---
layout: post
title: "git, what have I done?"
date: 2014-03-08
comments: true
categories: [git]
---

Git, the superhero of version control, allows you to review all the commands that have modified the HEAD tag in a git repo: ```git reflog```

The output for one of my demo apps:
{% codeblock Git, you're my hero lang:bash %}
5075d26 HEAD@{0}: checkout: moving from master to kleonard/add_skyline_background
531c396 HEAD@{1}: pull: fast forward
435f6de HEAD@{2}: commit: Adds logic for running and testing
043e35a HEAD@{3}: commit: Adds logic to process input
5155ba4 HEAD@{4}: commit: Adds input file
a629871 HEAD@{5}: rebase finished: returning to refs/heads/kleonard/create_input_files
a010021 HEAD@{6}: rebase: Add input file
b7cd86a HEAD@{7}: commit: Initial commit arbitrage
5ed00ea HEAD@{8}: commit: Adds launcher
7b2d720 HEAD@{9}: commit: Adds logic to find smallest number of moves
9c0dfce HEAD@{10}: commit: Adds implementation for bin creation
{% endcodeblock %}

Hmmm... looks like my commit messages need a little work... Still, it is a great tool for catching up on the changes to a repo (especially if you haven't touched it in a while), finding out where the bulk of the work is happening, or just reminding yourself what you did yesterday.

Awesome.