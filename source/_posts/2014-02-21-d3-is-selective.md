---
layout: post
title: "d3 is selective"
date: 2014-02-21
comments: true
categories: [javascript, d3]
---

D3 uses the W3C standard selector library, and selection modifiers work on both single and multiple element selections, and will automatically apply the modifier iteratively over the entire collection, which makes your code simpler and easier to maintain.
<!--more-->

####Selection modifiers
* **d3.select('p').text()**     : retrieve or modify text
* **d3.select('p').attr()**     : retrieve or modify a given attribute
* **d3.select('p').classed()**  : retrieve or modify css classes
* **d3.select('p').style()**    : retrieve or modify styles
* **d3.select('p').html()**     : retrieve or modify a selections inner html (like text but with tags)

```
d3.selectAll('div')
            .attr("class", "alert")
            .each(function(d, i) {
              d3.select(this).append("h1").text(i);
            });
```

Selections are essentially arrays with some extra variables baked in. The ```.each``` call on line 3 treats the selected elements as an array, and performs a function on each one. The extra variables provided to the iterator function for free are: the data bound to the element (d), the index of the element, and the element itself (this).

Awesome