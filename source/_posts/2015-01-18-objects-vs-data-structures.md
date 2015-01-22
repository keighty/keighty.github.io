---
layout: post
title: "objects vs data structures"
date: 2015-01-18
comments: true
categories: [ruby, rails]
---

"It is impossible to create an abstraction unknowingly or by accident," says Sandi Metz in [*Practical Object Oriented Design in Ruby*](http://www.amazon.com/Practical-Object-Oriented-Design-Ruby-Addison-Wesley/dp/0321721330/ref=sr_1_1?ie=UTF8&qid=1421723392&sr=8-1&keywords=practical+object+oriented+design+in+ruby). An abstraction is a common, stable quality, such that you would find in a java interface. An interface is an idea that cannot be made concrete, but contains behaviour [encoding similarities](http://en.wikipedia.org/wiki/Interface_%28Java%29) which objects might share. Even the definition of abstraction is abstract, and it wasn't until I read Robert C. Martin's chapter on Objects and Data Structures in [*Clean Code*](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) that I really started to understand them.
<!--more-->

### What is a data structure
A data structure is a class that exposes its data to the public. It should have no meaningful behaviour, and can be characterized by a set of attributes along with getters and setters.

```ruby
class Point1
  attr_accessor :x, :y, :z
end

p = Point1.new
p.x = 1
p.y = 1
p.z = 1
p
```

In this example, a Point is defined as an object at some location on a coordinate grid of some kind. That sounds abstract enough: you can create a point, {% pullquote %}access all of the point's data, and set its location along each axis independently. You have an instance of a point, but it is not an abstraction of a point -- it is a data structure. {"Hiding implementation is not about putting a layer of functions between the variables. It is about abstractions."}
{% endpullquote %} Adding an initializer that sets each variable, or a `to_s` method for getting a pretty output, would not make the Point1 class any more an abstraction of a position in space. To make an abstraction you can't just use getters and setters -- you have to think about how you are representing the data.

### What is an object
An object hides its data behind abstractions. A Point is not defined by its `[@x, @y, @z]`, but by its location in space. If space is a coordinate system, it could be 2- or 3-dimensional.

```
class Point2
  def initialize(x, y, z = nil)
    set_location(x, y, z)
  end

  def to_s
    "(#{[@x, @y, @z].compact.join(',')})"
  end
  alias_method :location, :to_s

  def set_location(x, y, z = nil)
    @x = x
    @y = y
    @z = z
    location
  end
  private :set_location

  alias_method :move_point, :set_location
  public :move_point

end

p = Point2.new(1, 1)
p.move_point(2, 3)
p.move_point(2, 3, 7)
```

A point cannot exist separate from it's coordinates, and coordinates cannot exist or be altered without using the abstraction of moving in space.

> "We do not want to expose the details of our data. Rather we want to express our data in abstract terms. This is not merely accomplished by using interfaces and/or getters and setters. Serious thought needs to be put into the best way to represent the data that an object contains."

> -- Martin

This is why [fat models are an anti-pattern in Rails](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/). ActiveRecord makes an object out of a data structure, and adding behaviour beyond data validation makes the model a hybrid data object. [Service Objects](http://railscasts.com/episodes/398-service-objects) are abstractions of model behaviour (pun intended).

Now I know what an abstraction is, and I understand that simply extracting methods into interfaces doesn't magically make an abstraction. As Metz says, "good design naturally progresses toward small independent objects that rely on abstractions." An object is more than a data structure; it is an idea and it must be applied deliberately.


<!-- resources
http://www.cgore.com/programming/ruby/public-aliases-of-private-methods.lisp
-->
<!-- resources
http://railscasts.com/episodes/398-service-objects
http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/
-->