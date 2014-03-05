---
layout: post
title: "javascript dates"
date: 2014-03-01
comments: true
categories: [I didn't know that, javascript]
---

Javascript Date objects have zero indexed months but not dates.

```
> (new Date(2014, 0, 1)).strftime("%b %d, %Y")
"Jan 1, 2014"
> (new Date(2013, 11, 31)).strftime("%b %d, %Y")
"Dec 31, 2013"
> (new Date(2014, 0, 0)).strftime("%b %d, %Y")
"Dec 31, 2013"
```

Wat?

<!--more-->
###Resources
[Javascript strftime](http://tech.bluesmoon.info/2008/04/strftime-in-javascript.html)

