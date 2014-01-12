---
layout: post
title: "jQuery objects vs strings"
description: ""
category: jQuery
tags: []
---

## Object blocker!!

My pair was stuck for the longest time on the following code snip:

{% highlight javascript %}
$(function(){
  var new_square = '$(<div class="square"></div>)';

  $('body').on('click','.square', function(){
    // var my_square = $(this);
    $(this).toggleClass('blue');

    b = $(new_square)
      .insertAfter(this);
    a = $(new_square)
      .insertBefore(this);
    b.toggleClass('blue');
    a.toggleClass('blue');
  });
});
{% endhighlight %}
 We expected out output to include three elements -- the original square, one
 inserted after, and one inserted before. The output we were seeing included
 only two squares: the original one and the square inserted before. If
 we inverted the insert calls, we would see only one inserted after. We
 were totally stumped until we printed out the content of new_square:
 {% highlight javascript %}
 $('<div class="square"></div>');{% endhighlight %}
 We were actually passing a reference to a jQuery OBJECT into the insert methods,
 so in essence, we were creating the object once and then passing it around to
 different locations. When we changed new_square from a jQuery object:
 {% highlight javascript %}
 var new_square = '$(<div class="square"></div>)'; {% endhighlight %}
 to a string
 {% highlight javascript %}
 var new_square = '<div class="square"></div>'; {% endhighlight %}
 We saw our third element appear no problem.