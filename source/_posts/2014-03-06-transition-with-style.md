---
layout: post
title: "transition with style"
date: 2014-03-06
comments: true
categories: [d3, javascript]
---
Making things zoom across the page is what I used to think javascript was all about, but it wasn't until I started learning d3 that I found out how to make it happen.
<!--more-->
{% codeblock Simple Transition lang:js %}
var body = d3.select("body"),
    duration = 2000;

body.append("div")
        .style("background-color", "white")
        .style("margin", "40px")
        .style("width", "200px")
        .style("height", "200px")
        .style("border", "black solid thin")
    .transition()
    .duration(duration)
        .style("background-color", "black")
        .style("margin-left", "600px")
        .style("width", "100px")
        .style("height", "100px");
{% endcodeblock %}

D3 is smart enough to select an appropriate scale for the property in transition. If a transitioning element is not given an explicit starting point, D3 will use the computed style. If the end value is missing, the property will be treated as a constant. In the example above, the border stays the same, as do the top and bottom margins. The only properties that change are the ones specified below the transition().

Awesome.

###Resources
[Data Visualization with D3.js Cookbook](http://www.amazon.com/gp/product/178216216X/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=178216216X&linkCode=as2&tag=bridgeforpoke-20) (affiliate link)