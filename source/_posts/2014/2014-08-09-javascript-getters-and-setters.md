---
layout: post
title: "javascript getters and setters"
date: 2014-08-09
comments: true
categories: [javascript]
---

Getters and setters make sense in java, and I just learned that there is an equivalent pattern in javascript:

{% raw %}
```javascript
> var favouriteBook = {
  _title: "Catch-22",
  get title() {
    console.log("Getting title", this._title);
  },
  set title(value) {
    this._title = value;
    console.log("Setting title", this._title);
  }
}
> favouriteBook.title
 Getting title Catch-22

> favouriteBook.title = "Slaughterhouse 5"
 Setting title Slaughterhouse 5

> favouriteBook.title
 Getting title Slaughterhouse 5
```
{% endraw %}

The `get` and `set` key words are reserved for accessing or mutating a data property (instance variable). Of course, one can get and set data properties directly:

{% raw %}
```javascript
> favouriteBook.title
"Catch-22"

> favouriteBook.title = "Slaughterhouse 5"
> favouriteBook.title
"Slaughterhouse 5"
```
{% endraw %}

The advantage of using a getter and setter is that if a setter is defined without a getter, one can change the value of the data property, but can never read it. If a getter is defined without a setter, then the variable can be read but not be changed:

{% raw %}
```javascript
> var favouriteBook = {
  _title: "Catch-22",
  get title() {
    console.log("Getting title", this._title);
  }
}

> favouriteBook.title
Getting title Catch-22

> favouriteBook.title = "Slaughterhouse 5"

> favouriteBook.title
Getting title Catch-22
```
{% endraw %}

The setter function will fail silently, which makes troubleshooting difficult but ensures that the property remains undisturbed.

### Resources
[The principles of object-oriented javascript](http://www.amazon.com/Principles-Object-Oriented-JavaScript-Nicholas-Zakas/dp/1593275404/ref=sr_1_1?ie=UTF8&qid=1407339036&sr=8-1&keywords=principles+of+object+oriented+javascript)
