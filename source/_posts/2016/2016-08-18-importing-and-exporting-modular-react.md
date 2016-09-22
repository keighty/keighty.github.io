---
layout: post
title: "importing and exporting: modular React"
date: 2016-08-18
sharing: false
categories: [javascript, ES6, React]
published: true
---

While working on rewriting my side-project (VirtualPlaybill) in React, I got hung up on a very basic plumbing concept: named exports. <!--more-->

With ES5 you can export in three ways and import in one:
```javascript
// EXPORT STYLE 1
module.exports = {
  crunchy: function () { console.log('CRUNCHY!') },
  bacon: function () { console.log('BACON!') }
};

// EXPORT STYLE 2
exports.crunchy = function() { console.log('CRUNCHY!') }
exports.bacon = function() { console.log('BACON!') }

// EXPORT STYLE 3 (and my personal favourite)
var breakfast = {
  crunchy: function () { console.log('CRUNCHY!') },
  bacon: function () { console.log('BACON!') }
}
module.exports = breakfast
```

An export is a meaningful bundle of code. Importing takes a single form, providing a handle to the exported bundle:

```javascript
// IMPORT
var breakfast = require('breakfast.js')

breakfast.crunchy() // CRUNCHY!
breakfast.bacon() // BACON!
```

In ES6 there is a lot more flexibility: exports can be named and imports can take many forms.

#### Named exports
Importing a named export allows you to grab only the functions you need from a module, leaving the rest behind. Use curly braces to import a named export:
```javascript
//-----test.js-----//
export crunchy = () => { console.log('CRUNCHY!') }
export bacon = () => { console.log('BACON!') }

// IMPORT BY NAME
//-----main.js-----//
import { crunchy } from './test'
crunchy() // CRUNCHY!
bacon() // Uncaught ReferenceError: bacon is not defined(…)
```

#### Default exports
In addition to named exports, you can specify one `default` export per file. Simply importing that file provides a reference to the default export:

```javascript
//-----test.js-----//
export crunchy = () => { console.log('CRUNCHY!') }
default export bacon = () => { console.log('BACON!') }

// IMPORT BY DEFAULT
//-----main.js-----//
import myFunc from './test'
myFunc() // BACON!
```

You can also import both default and named exports at the same time:
```javascript
import myFunc, { crunchy } from './test'
crunchy() // CRUNCHY!
myFunc() // BACON!
```

For even more import options, checkout the section on imports from the [MDN docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import):

```javascript
import defaultMember from "module-name";
import * as name from "module-name";
import { member } from "module-name";
import { member as alias } from "module-name";
import { member1 , member2 } from "module-name";
import { member1 , member2 as alias2 , [...] } from "module-name";
import defaultMember, { member [ , [...] ] } from "module-name";
import defaultMember, * as name from "module-name";
import "module-name"
```

name
> Name of the object that will receive the imported values.

member, memberN
> Name of the exported members to be imported.

defaultMember
> Name of the exported default to be imported.

alias, aliasN
> Name of the object that will receive the imported property

module-name
> The name of the module to import. This is a file name.

#### What you CAN'T do...
... is use ES5 syntax and expect the import to know what you mean:

```javascript
//------my-component.js-------//
class MyComponent extends React.Component {
  render() {
    return <p>this is an awesome react component </p>
  }
}

export default MyComponent

//------main.js-------//
import MyComponent from './my-component'
console.log(MyComponent) // undefined
```

I was trying to write ES5 in ES6! Without an explicit `export` in front of the class declaration, my default had no idea what I intended to export, so it did nothing. Adding `export` to the class definition, or even moving the whole `export default` to the class declaration does the job:

```javascript
//------my-component.js-------//
default export class MyComponent extends React.Component {
  render() {
    return <p>this is an awesome react component </p>
  }
}

//------main.js-------//
import MyComponent from './my-component'
console.log(MyComponent) // function MyComponent() {...}
```

Thanks to [this stackoverflow answer](http://stackoverflow.com/a/31853887) for pointing me in the right direction, and [exploringjs](http://exploringjs.com/es6/ch_modules.html) for even greater detail!
