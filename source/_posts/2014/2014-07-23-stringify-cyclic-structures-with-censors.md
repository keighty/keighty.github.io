---
layout: post
title: "stringify cyclic structures with censors"
date: 2014-07-23
comments: true
categories: [javascript]
---

Troubleshooting a d3 data visualization sometimes requires visual inspection of the underlying JSON data structure. You can convert linear data structures to a JSON string using [`JSON.stringify`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)<!--more-->:

{% raw %}
```javascript JSON.stringify
> x = {}
> JSON.stringify(x)
"{}"
> x.y = ["1", "2", "3"]
> JSON.stringify(x)
"{"y":["1","2","3"]}"
> x.z = {a: ["b", "c"]}
> JSON.stringify(x)
"{"y":["1","2","3"],"z":{"a":["b","c"]}}"
```
{% endraw %}

Data structures that contain both upstream and downstream links between objects are cyclic structures. From the docs:

> JSON does not support cyclic structures.  Attempting to convert such an object into JSON format will result in a TypeError exception.

{% raw %}
```javascript
> x = {}
  y = {}
  x.children = [y]
  y.parent = x
Object {children: [Object]}

> JSON.stringify(x)
TypeError: Converting circular structure to JSON
```
{% endraw %}

How can I look at a tree, or double-linked list data structure that is rife with circular links? JSON.stringify() takes a second argument called a censor in the docs. The censor is the programmatic court of appeal for whenever a cyclic structure is encountered:

{% raw %}
```javascript
function censor(key, value) {
  if (typeof value === "object") {
    return undefined;
  }
  return value;
}
```
{% endraw %}

Of course, this approach will eliminate most of the tree structure by removing all objects from the JSON. Certainly we can return something more creative than `undefined`...

{% raw %}
```javascript
function censor(key, value) {
  if (value && typeof value === "object" && value.parent) {
    value.parent = value.parent.name;
  }
  return value;
}
```
{% endraw %}

In this censor, we replace the parent object with the parent's name, and JSON.stringify has no complaints:

{% raw %}
```javascript
> function censor(key, value) {
  if (value && typeof value === "object" && value.parent) {
    value.parent = value.parent.name;
  }
  return value;
}

> x = {name: "Theodore"}
Object {name: "Theodore"}
> y = {name: "Alvin"}
Object {name: "Alvin"}
> x.children = [y]
[Object]
> y.parent = x
> Object {name: "Theodore", children: Array[1]}

> JSON.stringify(x)
TypeError: Converting circular structure to JSON

> JSON.stringify(x, censor)
"{"name":"Theodore","children":[{"name":"Alvin","parent":"Theodore"}]}"
```
{% endraw %}

Awesome.