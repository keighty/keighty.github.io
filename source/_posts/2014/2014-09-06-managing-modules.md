---
layout: post
title: "gaining closure through modules"
date: 2014-09-06
comments: true
categories: [javascript]
---

If you have more than one javascript running on a page, you have likely experienced the hell of trying to debug a problem caused by naming collisions. Developing the scripts in isolation, it makes sense to assign widgetA.name dynamically using jQuery, but then why does the title of widgetGraphB disappear? Avoid the pain of unintended consequences by namespacing your scripts. <!--more-->

Closures occur when you pass functions to variables to call at a later date. The javascript engine does all the work of compiling the script and loading the contents into the memory stack, but until the function is called the stack just sits there, sealed like a time capsule.

{% codeblock Closure in the wild lang:js %}
function timeCapsule() {
  var year = 1999;
  function party() {
    console.log("We're gonna party like it's " + year);
  }

  return party;
}

var year = 2099;
var tonight = timeCapsule();

tonight(); // We're gonna party like it's 1999
{% endcodeblock %}

The `party()` function has a closure over `timeCapsule()`, which means all the variables and functions within timeCapsule remain unmolested by any assignments made outside of their scope.

### Modules use closure to encapsulate scope.
Breaking your monolithic javascript into modules is a great way to separate concerns and create closures.

{% codeblock myModule.js lang:js %}
function myModuleFunction() { /* Do work1 */ }
function mySecondModuleFuntion() { /* Do work2 */ }

exports.myModuleFunction = myModuleFunction;
exports.mySecondModuleFunction = mySecondModuleFunction;
{% endcodeblock %}

{% codeblock Using the module lang:js %}
var myModule = require("myModule");

myModule.myModuleFunction(); // performs work1
myModule.mySecondModuleFunction(); // performs work2
{% endcodeblock %}

Using modules will prevent naming conflicts on functions and variables:

{% raw %}
```javascript
var module1 = require("myModuleName");

myModuleFunction() {
  console.log("All work and no play makes module1 a dull script;");
}

module1.myModuleFunction(); // performs work1
myModuleFunction(); // outputs snarky phrase

```
{% endraw %}

Voila -- no conflicts.