---
layout: post
title: "functional arguing"
date: 2014-02-20
comments: true
categories: [javascript, d3]
---

The ```arguments``` variable is a hidden parameter in javascript functions that contains all the arguments for the invocation in a pseudo-array (ie: it does not have array functions other than length and indexing).

```javascript
instance.headline = function (h) {
  if (!arguments.length) return headline;
  headline = h;
  return instance;
};
```
Even though the function specifically calls for an argument (```h```), it can still be called without one. This construction has a ruby equivalent, ```attr_accessor :headline```, which behaves as a getter and a setter, depending on the arguments provided (none, or ```headline = new_value```).

This "variable-parameter" function design is what enables method chaining. When the function is called as a getter it returns a string, but when it is called as a setter it returns the invoking object, passing itself along to the next method:

```javascript
var widget = SimpleWidget({ color: "#6439ed" })
                .headline("SimpleWidget")
                .description("This is a simple widget")
                .render();
```

<!--more-->

###Resources
[Data Visualization with D3.js Cookbook](http://www.amazon.com/gp/product/178216216X/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=178216216X&linkCode=as2&tag=bridgeforpoke-20) (affiliate link)
