---
layout: post
title: "octopress reloaded"
date: 2015-01-03
comments: true
categories: [blog]
---

I recently upgraded my home hardware from an [Asus 10.1" netbook](http://www.amazon.com/ASUS-1000HE-10-1-Inch-Black-Netbook/dp/B001QTXL82) to a [15" MacBook Pro](https://www.apple.com/macbook-pro/), and once I adjusted to being able to open more than one window at a time, I got down to the business of loading my files and projects on the new machine. Thanks to [Dropbox](https://www.dropbox.com/) and [Github](https://github.com/), most of that work happens either behind the scenes or as needed. Recreating the right environment for each project is still ongoing, and the first challenge I encountered was reloading my blog -- started about two years ago using [Octopress](http://octopress.org/).
<!--more-->

Octopress is an awesome framework built on [jekyll](http://jekyllrb.com/). The learning curve for getting started with jekyll was a little steep (liquid layouts, yaml, not to mention design and UX were all new to me then) , and Octopress delivers on its promise to make it easy: just clone, bundle, and blog. Part of what makes Octopress so awesome is the deploy strategy: static pages are generated from yaml and markdown sources (saved in the source branch), and the pages are checked in to your github or heroku repository (as the master branch).

When you clone the repo, you get both branches, but you have to do a little setup to ensure that master is pulling from and pushing to the right place. Borrowing some code from the [setup rake task](https://github.com/imathis/octopress/blob/master/Rakefile#L351-L358):

```bash
$ git clone git@github.com:username/username.github.com.git my_blog
$ cd my_blog
$ git checkout source
$ mkdir _deploy
$ cd _deploy
$ git init
$ git remote add origin git@github.com:username/username.github.com.git
$ git pull origin master
$ cd ..
$ bundle install
$ bundle exec rake preview
```

Now I have no excuses to not blog more often... well, fewer excuses (thanks to [Daniel Doubrovkine](http://code.dblock.org/octopress-setting-up-a-blog-and-contributing-to-an-existing-one) for pointing me in the right direction).

I briefly considered switching to a hosted blogging platform (tumblr or wordpress), so I conducted an informal survey (sample size taken from [Digital Ocean's 20 developers to follow](https://www.digitalocean.com/company/blog/20-developers-to-follow-in-2014/)) to see what choices leading developers were making, and it looks like a jekyll based blog is still the most popular. What is yours?


<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;}
.tg .tg-s6z2{text-align:center}
</style>
<table class="tg">
  <tr>
    <th class="tg-031e">platform</th>
    <th class="tg-031e">users</th>
  </tr>
  <tr>
    <td class="tg-s6z2">jekyll</td>
    <td class="tg-031e">6</td>
  </tr>
  <tr>
    <td class="tg-031e">octopress</td>
    <td class="tg-031e">3</td>
  </tr>
  <tr>
    <td class="tg-031e">none</td>
    <td class="tg-031e">3</td>
  </tr>
  <tr>
    <td class="tg-031e">tumblr</td>
    <td class="tg-031e">1</td>
  </tr>
  <tr>
    <td class="tg-031e">hand-rolled</td>
    <td class="tg-031e">6</td>
  </tr>
  <tr>
    <td class="tg-031e">wordpress</td>
    <td class="tg-031e">1</td>
  </tr>
</table>
