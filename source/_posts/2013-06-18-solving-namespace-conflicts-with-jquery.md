---
layout: post
title: "Namespaces and jQuery"
description: "solving namespace conflicts with jquery"
category: jQuery
tags: []
---

### Namespaces in jQuery getting you down?
Chuck taught me a great trick today -- how to avoid namespace
conflicts in jQuery:
{% highlight javascript %}
(function($){
...
})(jQuery);
{% endhighlight %}
This function is being passed jQuery as an argument, and is
assigning it the local variable $ -- allowing you to use
the $ and reference the jQuery library.

Awesome.
