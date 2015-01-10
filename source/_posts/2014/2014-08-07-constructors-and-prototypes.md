---
layout: post
title: "constructors and prototypes"
date: 2014-08-07
comments: true
categories: [javascript]
---

Object-oriented programming (OOP) can be thought of as describing the properties and behaviour of a noun in a blueprint, which is used to create instances of an object. The blueprint is defined as a Constructor -- a function that identifies a class of objects and begins with a capital letter. New objects are created by calling a Constructor with the keyword `new`.

{% raw %}
```javascript
> function Book(title, author) {
    this.title = title;
    this.author = author;
    this.listing = function() {
      console.log([this.title, "by", this.author].join(' '));
    }
  }

> var book1 = new Book("Catch-22", "Joseph Heller")

> book1.title
"Catch-22"

> book1.author
"Joseph Heller"

> book1.listing()
Catch-22 by Joseph Heller
```
{% endraw %}

The Book constructor specifies two properties, `title` and `author`, and one method, `listing`. Defining a method in a constructor ensures that all instances of the Object have the same method, but it also results in duplication of the method in the memory stack for every instance of the Object. If I made 1000 instances of Book, there would be 1000 instances of the listing method, even though it is only defined once, and is the same for every object.

####Memory stack of objects with instance methods:
<p><img src="/images/post_constructor_memory_stack.png"></p>

Enter Prototypes.

A Prototype is a property that is shared by all instances of an object. When the constructor for an object is called for the first time, all the object's properties are loaded onto the memory stack as well as the object's Prototype. All subsequent instances of that object include a pointer to the same Prototype, and they pass in a pointer to themselves as `this` whenever they call a method from the Prototype.

{% raw %}
```javascript
function Book(title, author) {
  this.title = title;
  this.author = author;
}

Book.prototype = {
  listing: function() {
    console.log([this.title, "by", this.author].join(' '));
  }
}

> var book1 = new Book("Catch-22", "Joseph Heller")
> var book2 = new Book("Slaughterhouse 5", "Kurt Vonnegut")

> book1.listing()
Catch-22 by Joseph Heller
> book2.listing()
Slaughterhouse 5 by Kurt Vonnegut

> book1.hasOwnProperty("listing")
false
> 'listing' in book1
true
```
{% endraw %}

####Memory stack of object with prototypes:

<p><img src="/images/post_prototype_memory_stack.png"></p>

Reduced memory consumption is one advantage of keeping a reference to a prototype instead of a complete copy. Another advantage is that if additions are made to the Prototype, the changes will be extended to all instances.

Awesome.