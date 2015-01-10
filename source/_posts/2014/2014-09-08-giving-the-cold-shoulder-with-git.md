---
layout: post
title: "giving the cold shoulder with git"
date: 2014-09-08
comments: true
categories: [git]
---

Some files become unnecessary to track using version control, but still belong in the repository. Compiled assets, for example may require a placeholder for a functional deploy, but don't need to be checked in every time a change is made.

Adding the file to your gitignore is not enough to stop tracking file changes. You must also clear the file from your git cache:

{% codeblock gitignore, this time with spite lang:bash %}
$ git rm --cached [filename]
$ git add .
$ git commit -m "I really mean to ignore this file."
{% endcodeblock %}

To untrack everything that has been added to the .gitignore:

{% codeblock For realz lang:bash %}
$ git rm -r --cached .
$ git commit -a -m "I mean it!"
{% endcodeblock %}

### Resources
[Here's where I found this gem](http://stackoverflow.com/a/1139797),
 [and here are the docs where I verified it](http://git-scm.com/docs/gitignore).
