---
layout: post
title: "jQuery-UI and meteor"
date: 2014-06-16
comments: true
categories: [javascript, meteor]
---

I have been working on [Virtual Playbill](virtualplaybill.net) for the last few weeks using meteor, bootstrap, and a number of other UI packages. I wanted to use a datepicker as a form field, but getting jQuery-ui working was not a straightforward package download as many other features are. There are currently 5 packages available for installation on [Atmosphere](https://atmospherejs.com/package/jquery-ui?q=jquery-ui) (the meteor package manager), and I attempted several of them before finding the combination that worked<!--more-->:

###1. Install the jQuery-ui package from [Atmosphere](https://atmospherejs.com/package/jquery-ui)
```
mrt add jquery-ui
```

###2. Download jQuery-ui
For me, the meteorite package did not properly install the css or images I needed to get the datepicker working, so I downloaded [jquery-ui](http://jqueryui.com/), swiped the css file (un-minified), and added it to my `client/stylesheets` folder.

###3. Add jQuery-ui images
The css styled the datepicker calendar perfectly, except for the previous month and next month buttons. jQuery-ui comes with a few standard icons, so I placed the jQuery-ui images folder in `/public`, since static files must be kept in a public folder for meteor to acknowledge them.

###4. Relocate the images
The jQuery-ui css file tries to locate the images in its parent folder, but as I mentioned, static assets = public folder. The advantage of the public folder is that all assets can be referenced with a leading slash: `/images`. I scanned the un-minified version of the css and changed the image url locations from `images/...` to `/images/...`

Voila! A beautiful, functional datepicker!

<img src="{{ root_url }}/images/post_datepicker.png" />