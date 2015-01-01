---
layout: post
title: "delay chained transitions"
date: 2014-03-15
comments: true
categories: [d3, javascript]
---

Changing an element using multiple, chained transitions requires a little troubleshooting. You would think that just chaining them one after another would suffice:

{% codeblock Chained Transitions lang:js %}
selection.transition().duration(1500)
    .attr("cx",getX)
    .attr("cy",getY)
    .attr("r", getR)
    .style("fill", getColor)
  .transition().duration(1500)
    .attr("r", doubleR);
{% endcodeblock %}

But D3 does not wait for one transition to be complete before it begins the next one. Adding a delay will accomplish the desired transition chaining:

{% codeblock ACTUAL Chained Transitions lang:js https://gist.github.com/keighty/9508948 Gist %}
selection.transition().duration(1500)
    .attr("cx",getX)
    .attr("cy",getY)
    .attr("r", getR)
    .style("fill", getColor)
  .transition().delay(1700).duration(1500)
    .attr("r", doubleR);
{% endcodeblock %}

<div id="showme">
<style>
.axis path,
.axis line {
    fill: none;
    stroke: black;
    shape-rendering: crispEdges;
}

.axis text {
    font-family: sans-serif;
    font-size: 11px;
}
</style>
<script src="http://mbostock.github.com/d3/d3.v2.min.js"></script>
<script>

var width = 500,
    height = 500,
    margin = 50,
    data,
    dataFromCsv;

var scaleX=d3.scale.linear().domain([0,10]).range([margin,width-margin]);
var scaleY=d3.scale.linear().domain([0,10]).range([height-margin,margin]);

var svg=d3.select("#showme").append("svg")
      .attr("width",width)
      .attr("height",height);

function drawAxes() {
  var xAxis = d3.svg.axis()
    .scale(scaleX)
    .orient("bottom")
    .ticks(5)
    .tickSubdivide(1)
    .tickPadding(10)
    .tickFormat(function(d) { return d + " ticks"});

  var yAxis = d3.svg.axis()
    .scale(scaleY)
    .orient("left");

  svg.append("g")
    .attr("class", "axis")
    .attr("transform", "translate(0," + (height - margin) + ")")
    .call(xAxis);
  svg.append("text")
    .attr("class", "x label")
    .attr("text-anchor", "middle")
    .attr("x", width/2)
    .attr("y", height)
    .text("X-value");

  svg.append("g")
    .attr("class", "axis")
    .attr("transform", "translate(" + margin + ",0)")
    .call(yAxis);
  svg.append("text")
    .attr("class", "y label")
    .attr("text-anchor", "middle")
    .attr("x", -height/2)
    .attr("y", 6)
    .attr("dy", ".75em")
    .attr("transform", "rotate(-90)")
    .text("Y-value");
}

dataFromCsv = [{ "Color": "#9467bd", "X": 1, "Y": 8, "Quantity": 91 },
{ "Color": "#1f77b4", "X": 9, "Y": 1, "Quantity": 32 },
{ "Color": "#1f77b4", "X": 3, "Y": 1, "Quantity": 67 },
{ "Color": "#bcbd22", "X": 5, "Y": 5, "Quantity": 63 },
{ "Color": "#9467bd", "X": 6, "Y": 6, "Quantity": 57 },
{ "Color": "#2ca02c", "X": 5, "Y": 10, "Quantity": 65 },
{ "Color": "#d62728", "X": 8, "Y": 2, "Quantity": 36 },
{ "Color": "#9467bd", "X": 2, "Y": 8, "Quantity": 82 },
{ "Color": "#bcbd22", "X": 4, "Y": 7, "Quantity": 69 },
{ "Color": "#9467bd", "X": 5, "Y": 3, "Quantity": 88 },
{ "Color": "#d62728", "X": 1, "Y": 5, "Quantity": 29 },
{ "Color": "#1f77b4", "X": 10, "Y": 7, "Quantity": 60 },
{ "Color": "#2ca02c", "X": 8, "Y": 5, "Quantity": 56 },
{ "Color": "#bcbd22", "X": 1, "Y": 6, "Quantity": 31 },
{ "Color": "#bcbd22", "X": 6, "Y": 5, "Quantity": 57 },
{ "Color": "#d62728", "X": 9, "Y": 6, "Quantity": 85 },
{ "Color": "indigo", "X": 9, "Y": 10, "Quantity": 70 },
{ "Color": "indigo", "X": 9, "Y": 10, "Quantity": 31 },
{ "Color": "#1f77b4", "X": 2, "Y": 4, "Quantity": 26 },
{ "Color": "#bcbd22", "X": 10, "Y": 5, "Quantity": 61 },
{ "Color": "#2ca02c", "X": 4, "Y": 2, "Quantity": 64 },
{ "Color": "#1f77b4", "X": 8, "Y": 9, "Quantity": 71 }];
function importData() {
  data = d3.nest()
          .key(function(d) { return d.Color; })
          .rollup(aggregateData)
          .entries(dataFromCsv);
  // drawElements();
  setInterval( drawElements, 4000 );
}

/***********************
  Draw circles, bind data, and move elements to final location
***********************/
function drawElements() {
  var selection = svg.selectAll("circle").data(data);

  selection.enter()
    .append("circle")
    .attr("cx",scaleX(0))
    .attr("cy",scaleY(0))
    .attr("r", 0)

  selection.transition().duration(1500)
      .attr("cx",getX)
      .attr("cy",getY)
      .attr("r", getR)
      .style("fill", getColor)
    .transition().delay(1500+200).duration(1500)
      .attr("r", doubleR);
}

/***********************
  Helper functions
***********************/
function getX(d)     { return scaleX(d.values.X); }
function getY(d)     { return scaleY(d.values.Y); }
function getR(d)     { return d.values.Quantity / 10; }
function doubleR(d)     { return d.values.Quantity / 5; }
function getColor(d) { return d.key; }
// Use D3 built in array functions to aggregate data
function aggregateData(d) {
  return {
    "X"       : d3.mean(d, function(e) { return +e.X; }),
    "Y"       : d3.median(d, function(e) { return +e.Y; }),
    "Quantity": d3.max(d, function(e) { return +e.Quantity; })
  };
}

drawAxes();
importData();
</script>
</div>