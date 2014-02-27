---
layout: post
title: "binding functions as data"
date: 2014-02-27
comments: true
categories: [javascript, d3]
---

Primitives and object literals are not the only things you can bind to a D3 visualization. After all, aren't javascript functions objects as well?

```javascript
var data = [];
var next = function(x) { return 15 + x*x };
var newData = function() { data.push(next); return data; }
var selection = d3.select("#container")
                  .selectAll("div")
                  .data(newData);

...
                  .select("#span")
                    .text(function(d, i) { return d(i); });
```

If you provide a function to the data function, D3 will simply invoke the function and use the returned value as a parameter of the data function.

###Resources
[Data Visualization with D3.js Cookbook](http://www.amazon.com/gp/product/178216216X/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=178216216X&linkCode=as2&tag=bridgeforpoke-20) (affiliate link)