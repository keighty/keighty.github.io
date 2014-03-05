---
layout: post
title: "d3 nests and rollups"
date: 2014-03-04
comments: true
categories: [d3, javascript]
---

D3 provides powerful functions for manipulating raw data sets, including csv(), nest(), and rollup().
<!--more-->
###csv()
You can import a raw, comma-delimited data file, and bind the data directly to an svg.

```
d3.csv("test_data.csv", function(csv_data) {

  svg.selectAll("circle").data(csv).enter()
    .append("circle")
    .attr("cx",getX)
    .attr("cy",getY)
    .attr("r", getR)
    .style("fill", getColor);
})
```
<a href=""></a><img src="source/images/post_d3_csv_data.png" />
{% img source/images/post_d3_csv_data.png %}

###nest() and rollup()
You can also aggregate data on any property -- just tell D3 which one, and use any of D3's handy utility functions (mean, median, sum, min/max, quantile, etc) to return the aggregate data you want to display.

```
d3.csv("apples.csv",function(csv) {
  data = d3.nest()
          .key(function(d) { return d.Colors; })
          .rollup( function(d) {
            return {
              "X": d3.mean(d, function(e) { return +e.X; }),
              "Y": d3.mean(d, function(e) { return +e.Y; }),
              "Quantity": d3.mean(d, function(e) { return +e.Quantity; })
            };
          })
          .entries(csv);

  svg.selectAll("circle").data(data).enter()
    .append("circle")
    .attr("cx",getX)
    .attr("cy",getY)
    .attr("r", getR)
    .style("fill", getColor);

})
```
{% img source/images/post_d3_rollupdata_image.png %}

D3 is awesome.