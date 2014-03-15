---
layout: post
title: "easing does it"
date: 2014-03-12
comments: true
categories: [d3, javascript]
---

With the possible exception of UFOs, things don't usually switch from being stationary to moving at maximum velocity without an intervening period of acceleration or deceleration. Similarly, it can be difficult to track elements on a web page that move from one position to another with no warning, or faster than the human eye can perceive. Enter easing functions: <!--more-->

> "**Easing functions** specify the rate of change of a parameter over time." --Robert Penner

{% codeblock Easy easing lang:js https://gist.github.com/keighty/9508948 Gist %}
selection.enter()
  .append("circle")
  .attr("cx",scaleX(0))
  .attr("cy",scaleY(0))
  .attr("r", 0);
selection.transition().ease("cubic-in-out").duration(1500)
  .attr("cx",getX)
  .attr("cy",getY)
  .attr("r", getR)
  .style("fill", getColor);
{% endcodeblock %}

The transition will act on the selection in the following ways:

* it will move the element from its starting x position of 0 to the x position defined by ``getX()``
* it will move the element from its starting y position of 0 to the y position defined by ``getY()``
* it will increase the element's radius from 0 to the radius returned from ``getR()``
* it will gradually change the element's color using an ordinal color scale, from black to the color returned from ``getColor()``
* all of this movement will be performed with a "cubic-in-out" easing function

The cubic-in-out easing function is the standard D3 easing function. The element will increase velocity by a power of 3 until halfway down the path it is destined to trace. It will then decrease velocity by a power of 3 until it reaches its final location. There are many different types of easing functions. Check out the resources for more information, and ask your doctor which easing function is right for you!

###Resources
Robert Penner's [Easing Functions](http://www.robertpenner.com/easing/)

[Data Visualization with D3.js Cookbook](http://www.amazon.com/gp/product/178216216X/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=178216216X&linkCode=as2&tag=bridgeforpoke-20) (affiliate link)
