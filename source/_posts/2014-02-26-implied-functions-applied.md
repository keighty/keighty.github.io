---
layout: post
title: "implied functions applied"
date: 2014-02-26
comments: true
categories: [I didn't know that, javascript, d3]
---

When binding a primitive datum to a node in a D3 visualization, the callback function can be implicitly called on the datum without the typical verbosity<!--more-->:

```javascript
svg.selectAll("circle")
    .data([32, 57, 112, 293])
  .enter().append("circle")
    .attr("cy", 90)
    .attr("cx", String)
    .attr("r", Math.sqrt);
```

In line 5, String() is performed on the data element, as is Math.sqrt in line 6, with the same result as:

```
svg.selectAll("circle")
    .data([32, 57, 112, 293])
  .enter().append("circle")
    .attr("cy", 90)
    .attr("cx", function(d) {
      return String(d);
    })
    .attr("r", function(d) {
      return Math.sqrt(d);
    });
```

... but better.

Awesome.

###Resources
[Three Little Circles D3 Tutorial](http://mbostock.github.io/d3/tutorial/circle.html)
