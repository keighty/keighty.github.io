---
layout: post
title: "custom asides"
date: 2014-02-15
comments: true
categories: [octopress, jekyll]
---

I was following a [tutorial](http://blog.jmac.org/blog/2013/03/30/putting-twitter-back-into-octopress/) on how to include a twitter widget in the sidebar of my octopress site and couldn't figure out why my twitter content wouldn't render. It turns out that custom asides require an internal div. <!--more-->The process:

###1. Create a twitter timeline widget
This was surprisingly easy. Just login to [twitter.com](https://twitter.com/) and click the settings icon: <i class="fa fa-cog fa-2x"></i>

Click "Widgets" on the bottom of the left nav and follow the instructions.

CAKE WALK.

###2. Create a custom aside
Perhaps it is the tutorial showing its age, but I couldn't get the content to render as described. I grabbed the widget html and created a file in source/custom/asides:

```html twitter.html
<section>
  <h1>Twitter</h1>
  <!-- paste twitter widget code here -->
</section>
```

I looked to the other asides for help, and realized that the content had to be anchored to a div of its own:

```html div: my saviour
<section>
  <div>
    <h1>Twitter</h1>
    <!-- paste twitter widget code here -->
  </div>
</section>
```

###3. Add the aside to your _config.yml

```ruby What my _config.yml looks like
# list each of the sidebar modules you want to include, in the order you want them to appear.
# To add custom asides, create files in /source/_includes/custom/asides/ and add them to the list like 'custom/asides/custom_aside_name.html'
default_asides: [custom/asides/contactme.html, asides/recent_posts.html, asides/github.html, custom/asides/aboutme.html, custom/asides/twitter.html]
```

Awesome.