---
layout: post
title: "the value of slow transitions"
date: 2014-03-08
comments: true
categories: [d3, javascript, read]
---

###The Why
From [Object Constancy](http://bost.ocks.org/mike/constancy/) by Mike Bostock:
> Animated transitions are pretty, but they also serve a purpose: they make it easier to follow the data. This is known as __object constancy__: a graphical element that represents a particular data point... can be tracked visually through the transition. This lessens the cognitive burden by using **preattentive processing** of motion rather than sequential scanning of labels.

(The emphasis is mine)

###The How
Object constancy is achieved through object identity: the data is bound to it's object by a shared unique identifier. This allows D3 to reuse the object when the data set is updated. Object identity is achieved by passing a key function to the data() method at the time of data binding:

{% codeblock %}
var data = [ // <-A
        {id: 0, value: "foo"},
        {id: 1, value: "bar"},
        {id: 2, value: "foo"},
        {id: 3, value: "baz"},
        {id: 4, value: "bar"}
    ];
d3.selectAll("svg:g")
    .data(data, function(d){return d.id;});
{% endcodeblock %}