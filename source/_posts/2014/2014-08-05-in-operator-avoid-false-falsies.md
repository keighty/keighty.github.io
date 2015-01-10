---
layout: post
title: "'in' operator avoids false falsies"
date: 2014-08-05
comments: true
categories: [javascript]
---

I have encountered more than my share of javascript errors because I have made assumptions about the object I am working with, such as assuming that an object contains a property, and that I can call methods on that property:

{% raw %}
```javascript
> myObject.data[0]
TypeError: Cannot read property '0' of undefined
```
{% endraw %}

This line contains four assumptions, and therefore four places it can fail:

1. `myObject` exists
2. `myObject` contains a property called `data`
3. `data` is an array
4. `data` is non-empty

Presuming that if `myObject` does not exist means you have bigger problems, it is easy to fall into the trap of checking for the existence of a property by gating a conditional with it:

{% raw %}
```javascript
if(myObject.data) {
  //do something important with the data
}
```
{% endraw %}

This technique will work in development, but the voice of experience tells us that it will not work in production. The reason it will not work is that javascript has more falsy values than most languages. Falsy values include the number zero, empty strings, null, undefined, NaN, and of course, the boolean false. So, if it is possible `myObject.data` to contain derived information, you have signed up to troubleshoot some unexpected behaviour.

Fortunately, javascript has provided a failsafe way to check the existence of a property, regardless of the data it contains. The `in` operator checks if the key exists in the object's hash table, and doesn't care if it is a prototype or singleton property.

{% raw %}
```javascript
> var myObject = {}
undefined
> 'data' in myObject
false

> myObject.data = ''
""
> 'data' in myObject
true
> myObject.data == true
false
```
{% endraw %}

`('data' in myObject)` returns false if the property is undefined. If the property is defined, it returns true even if data contains a falsy value.

### Resources
[The principles of object-oriented javascript](http://www.amazon.com/Principles-Object-Oriented-JavaScript-Nicholas-Zakas/dp/1593275404/ref=sr_1_1?ie=UTF8&qid=1407339036&sr=8-1&keywords=principles+of+object+oriented+javascript)

