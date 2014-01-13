---
layout: post
title: "Show-off with GitHub and Docco"
comments: true
description: ""
category: web
tags: []
---

I was experimenting with some documentation styles today and discovered two bits of gold: gh-pages and docco<!--more-->:

Creating a site for your project using GitHub is a 3 step process:
1. create a new orphan branch called gh-pages
2. remove all the contents
3. fill the branch with your boastful new site

{% highlight bash %}
$ cd repository

$ git checkout --orphan gh-pages
# Creates a branch, without any parents
# Switched to a new branch 'gh-pages'

$ git rm -rf .
# Remove all files from the old working tree
{% endhighlight %}

From here you can use the [GitHub Automatic Page generator](https://help.github.com/articles/creating-pages-with-the-automatic-generator) to throw up a theme and some standard content, you can roll out a [jekyll bootstrap](http://jekyllbootstrap.com/) platform with blogging integration, or you can simply:

{% highlight bash %}
$ echo "Hello gh-pages!" > index.html
$ git add index.html
$ git commit -a -m "First pages commit"
$ git push origin gh-pages
{% endhighlight %}

Add and commit your content to your gh-pages branch, and GitHub will generate what needs to be generated in order to serve your website.

Your pages are hosted for free by GitHub. If you have already configured a personal site using [User Pages](https://help.github.com/articles/user-organization-and-project-pages), your project pages will hang off the end of your domain like this : username.github.io/repo_name

###[Docco](http://jashkenas.github.io/docco/)
>Docco is a quick-and-dirty documentation generator, written in Literate CoffeeScript. It produces an HTML document that displays your comments intermingled with your code. All prose is passed through Markdown, and code is passed through Highlight.js syntax highlighting.

Docco works with  Python, Ruby, JavaScript, Java, and many other languages, with no configuration -- just plug and play!

Install Docco with npm:
{% highlight bash %}
sudo npm install -g docco
{% endhighlight %}

Run it against your code:
{% highlight javascript %}
docco lib/*.ruby
{% endhighlight %}

Docco will create a docs/ folder in your project and will generate an html file for each ruby file in the lib directory. This is where beautifully commented code really pays off, but docco does not format multi-line code the same way. Check out my [benchmark.rb](http://www.katieleonard.ca/PCSnotes/docs/benchmark.html) for an example of how each comment style is treated by docco.

TADA