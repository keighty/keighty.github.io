---
layout: post
title: "Hoisting with JavaScript"
comments: true
description: ""
category: javascript
tags: []
---

I am really enjoying [Test-Driven Development](http://www.amazon.com/Test-Driven-JavaScript-Development-Developers-Library/dp/0321683919) by Christian Johansen

I picked up this little gem this morning as I was learning about functions and the various objects associated with them:

You CANNOT use function declarations in conditionals. Take this example:
{% highlight java %}
if (String.prototype.trim) {
  function trim(str) {
    return str.trim();
  }
else {
  function trim(str){
    return str.replace(/^\s+|\s+$/g, "");
  }
}{% endhighlight %}

What is wrong with that, you might ask? HOISTING! Both functions are hoisted up to the global variable before execution, which means that the second one always overwrites the first one.

This is a better method:
{% highlight java %}
if(!String.prototype.trim) {
  String.prototype.trim = function trim() {
    return this.replace(/^\s+|\s+$/g, "")
  }
}{% endhighlight %}

This way, we are not overwriting any browser-available Trim methods, we are declaring the method on the String.prototype, which means it is available to ALL strings in scope, AND we have named it so that it will be more visible in a stack trace in case something goes wrong.

AWESOME