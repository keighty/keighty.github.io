---
layout: post
title: "Javascript has spies"
comments: true
description: ""
category: javascript
tags: [js, jasmine]
---

## Spies vs Mocks vs Stubs?

We are covering testing frameworks at PCS this week,
 particularly Jasmine and Sinon.

Jasmine has a similar structure to RSpec:
{% highlight java %}
describe("Class to describe", function() {
  it("should have some behavior". function() {
    //assertions
  });
});{% endhighlight %}

Also similar to RSpec, Jasmine allows you to spy on behavior.<!--more-->
 Using spies, I can watch a method call and collect information about it.

* Want to know if it was called?
{% highlight java %}expect(method).toHaveBeenCalled(){% endhighlight %}
* Want to know how many times?
{% highlight java %}expect(method.callCount).toEqual(3){% endhighlight %}
* Want to know how it was called?
{% highlight java %}expect(method).toHaveBeenCalledWith(args){% endhighlight %}

For example:
{% highlight java %}
describe( "Spy on methods", function () {
  it( "should watch calls to console", function () {
    var mySpy = spyOn(console, "log");
    console.log("test1");
    console.log("test2");
    console.log("test3");

    expect(mySpy.callCount).toEqual(3);
  });
});{% endhighlight %}
Tomorrow, I conquer Stubs and Mocks!