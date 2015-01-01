---
layout: post
title: "fun with ticks"
date: 2014-03-05
comments: true
categories: [d3, javascript]
---

D3 has an awesome tick library, which makes it easy to customize an axis. I have not found any evidence of a built in function to customize tick rendering depending on if it is a major or a minor tick. One way to get around that is to render two axes, and only label one<!--more-->:

{% codeblock axes ticks lang:js %}
var scaleX=d3.scale.linear().domain([0,10]).range([margin,width-margin]);

var svg=d3.select("body").append("svg")
      .attr("width",width)
      .attr("height",height);

var xAxis = d3.svg.axis()
  .scale(scaleX)
  .orient("bottom")
  .ticks(5)
  .tickSubdivide(5)
  .tickPadding(10)
  .tickFormat(function(d) { return d + " ticks"});

var x2Axis = d3.svg.axis()
  .scale(scaleX)
  .orient("bottom")
  .ticks(5)
  .tickSize(14)
  .tickFormat("");

svg.append("g")
  .attr("class", "axis")
  .attr("transform", "translate(0," + (margin) + ")")
  .call(xAxis);

svg.append("g")
  .attr("class", "axis")
  .attr("transform", "translate(0," + (margin) + ")")
  .call(x2Axis);
{% endcodeblock %}

###TADA
{% img /images/post_d3_scale_major_ticks.png %}

###More fun with ticks
* **orient()**: set which side of the axis will be ticked and labeled
* **ticks()**: set the number of major ticks
* **tickSubdivide()**: set the number of minor ticks
* **tickPadding()**: set the distance between the ticks and their label
* **tickFormat()**: set the units to appear after the label on the major ticks
* **tickSize()**: specify one arg for inner ticks, another argument for outer ticks, or use outerTickSize() or innerTickSize() to specify one or the other. Outer ticks are at the ends of the scales.
