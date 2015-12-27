---
layout: post
title: "javascript object property meta properties"
date: 2015-11-07
comments: true
sharing: true
categories: [javascript]
---

JavaScript Objects are complex types, and even defined properties have a control panel of their own. You can view the content of the control panel using `Object.getOwnPropertyDescriptor()`, and flip their switches using `Object.defineProperty()`. <!--more-->

> Note: These property descriptors are for `own properties` -- that is, properties that are directly defined on an object, not those that come along for the ride on the object's prototype chain.

### The property descriptor holds meta data

```
> var me = {
  name: "Katie"
}

> Object.getOwnPropertyDescriptor( me, "name" )
{
  value: "Katie", // the value associated with the property
  writable: true,   // true if the value of the property can be changed
  enumerable: true, // true if the property shows up during enumeration of the properties
  configurable: true // true if any of these meta properties can be changed
}
```

### Change the property value

```
Object.defineProperty( me, "name", {
  value: "keighty"
})

me.name // "keighty"
```

### Make the property read only

```
Object.defineProperty(me, "name", {
  writable: false
})

me.name = "k80"
me.name  //"keighty" -- actual value of me.name did not change
```

### Make the property hidden from for-loops (or any enumeration)

```
me.hobbies = ["bridge", "curling", "tap dancing"]
me // Object {hobbies: Array[3], name: "keighty"}

for(fact in me) {
 console.log(fact)
}
// All the properties defined on me are written to the console:
// name
// hobbies

Object.defineProperty(me, "name", {
 enumerable: false
})

for(fact in me) {
 console.log(fact)
}
// Actually.. only enumerable properties are written to the console:
// hobbies
```

### Freeze the property

```
Object.defineProperty(me, "name", {
 configurable: false
})

Object.getOwnPropertyDescriptor(me, "name")
// { value: "keighty",
//   writable: false,
//   enumerable: false,
//   configurable: false }

Object.defineProperty(me, "name", {
 writable: true
})

Uncaught TypeError: Cannot redefine property: name(…)
```

From [the docs](http://www.ecma-international.org/ecma-262/5.1/#sec-8.10): "The Property Descriptor type is used to explain the manipulation and reification of named property attributes. Use Object.defineProperty for fine-grained control over the visibility and mutability of your object's properties.