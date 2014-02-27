---
layout: post
title: "enter update exit"
date: 2014-02-25
comments: true
categories: [javascript, d3]
---

D3 is a data visualization tool that uses method chaining to produce big pictures with little code. The selection library performs a full Outer Join between the data (D) and the visual elements (V), and the enter-update-exit pattern performs work on subsets of this join<!--more-->:

###UPDATE: Inner Join (D union V)
```
selection.data(data_points)
```
data() binds data_points to the html or svg elements returned by the selection and returns the data not visualized.

###ENTER: Left excluding join (D/V)
```
selection.data(data_points).enter()
```
enter() returns data_points that are not yet represented visually by html or svg. Chain .append() to add a new element to the page.

###EXIT: Right excluding join (V/D)
```
selection.data(data_points).exit()
```
exit() returns html or svg elements that no longer have data associated with them. Chain .remove() to eliminate them, or .translate() to make them slide off the page.

###Resources
[Data Visualization with D3.js Cookbook](http://www.amazon.com/gp/product/178216216X/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=178216216X&linkCode=as2&tag=bridgeforpoke-20) (affiliate link)

[Visual Representation of SQL Joins](http://www.codeproject.com/Articles/33052/Visual-Representation-of-SQL-Joins)