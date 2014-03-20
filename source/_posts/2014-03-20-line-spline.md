---
layout: post
title: "line, spline"
date: 2014-03-19
comments: true
categories: [d3]
---

D3 lines are rendered using linear interpolation by default. This means that the line connecting a series of points will trace a direct path from one node to another:

<div id="graph-here">
  <script src="http://mbostock.github.com/d3/d3.v2.min.js"></script>
  <script>
var width = 400,
    height = 400,
    margin = 5,
    scalex = d3.scale.linear().domain([0, 7]).range([margin, width - margin]),
    scaley = d3.scale.linear().domain([0, 7]).range([height - margin, margin]);

var data = [
  [
      {x: 0, y: 5},{x: 1, y: 7},{x: 2, y: 5},
      {x: 3, y: 5},{x: 4, y: 3},{x: 6, y: 4},
      {x: 7, y: 2}
  ],
  [
      {x: 0, y: 3},{x: 1, y: 5},{x: 2, y: 3},
      {x: 3, y: 3},{x: 4, y: 1},{x: 6, y: 2},
      {x: 7, y: 1}
  ]
];

var straightLine = d3.svg.line()
        .interpolate("linear")
        .x(function(d){return scalex(d.x);})
        .y(function(d){return scaley(d.y);});
var smoothLine = d3.svg.line()
        .interpolate("cardinal")
        .x(function(d){return scalex(d.x);})
        .y(function(d){return scaley(d.y);});

var svg = d3.select("#graph-here").append("svg");

  svg.attr("height", height)
      .attr("width", width);

   svg.selectAll("path")
        .data(data)
      .enter()
        .append("path")
        .attr("class", "line")
        .attr("fill", "none")
        .attr("stroke", "blue")
        .attr("stroke-width", 2)
        .attr("d", function(d, i){
          if(i % 2 === 0) return straightLine(d);
          return smoothLine(d)
        });

    svg.selectAll("textPath").data(data)
      .enter()
        .append("text")
        .attr("x", function(d) { return scalex(d[d.length-2].x); })
        .attr("y", function(d) { return scaley(d[d.length-2].y); })
        .attr("text-anchor", "end")
        .text(function(d, i) {
          if(i % 2 === 0) return "Straight Lines";
          return "Smooth Lines";
        });

  </script>
</div>

The curved line is rendered with the cardinal interpolator, one of several options D3 provides. The curvature of the line is called a **spline**. Splines are tools used by architects to draw curved lines, and the term was adopted by mathematicians to describe smooth, piecewise polynomial approximation:

> Splines are curves, which are usually required to be continuous and smooth... [The] join points are called knots. Splines with few knots are generally smoother than splines with many knots; however, increasing the number of knots usually increases the fit of the spline function to the data. Knots give the curve freedom to bend to more closely follow the data. --[Wikipedia](http://en.wikipedia.org/wiki/Spline_(mathematics))
