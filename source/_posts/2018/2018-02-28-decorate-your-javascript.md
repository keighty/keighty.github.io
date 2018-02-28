---
layout: post
title: "decorate your javascript"
date: 2018-02-28
sharing: true
categories: [javascript]
---

Javascript decorators are a form of metaprogramming: they add functionality to classes and properties. Unlike the [GoF pattern](https://en.wikipedia.org/wiki/Decorator_pattern), where decorators modify instances of a class, Javascript decorators are run when the class, method, or property is installed, modifying all instances.

Decorators are useful for adding extra functionality to behaviours and properties that would otherwise look like boilerplate -- such as cacheing, access control, logging, or instrumentation.<!--more-->

### How does it work?

A decorator function runs before the object it decorates is installed on the prototype. When you define an undecorated object like this:

```javascript
class ExampleWithoutDecoration {
  doWork() {
    console.log('can\'t you see I\'m working here?')
  }
}
```

The Javascript engine creates an object and installs the `doWork` method on its prototype:

```javascript
Object.defineProperty(ExampleWithoutDecoration.prototype, 'doWork', {
  value: specifiedFunction,
  enumerable: false,
  configurable: true,
  writable: true
})
```

When you define a DECORATED method like this:

```javascript
class ExampleWithDecoration {

  @DecoratingIsFun
  doWork() {
    console.log('can\'t you see I\'m working here?')
  }
}
```

The Javascript engine saves some temporary state, runs the decorator function, and then installs the `doWork` method on the object's prototype.

```javascript
let methodDescription = {
  type: 'method',
  initializer: () => specifiedFunction,
  enumerable: false,
  configurable: true,
  writable: true
};

methodDescription = DecoratingIsFun(ExampleWithDecoration.prototype, 'doWork', methodDescription) || methodDescription

defineDecoratedProperty(ExampleWithDecoration.prototype, 'doWork', methodDescription);

function defineDecoratedProperty(target, { initializer, enumerable, configurable, writable }) {
  Object.defineProperty(target, { value: initializer(), enumerable, configurable, writable })
}
```

In this case, the `DecoratingIsFun` method is run with `this` set to the object prototype, and it has the opportunity to modify/return a methodDescription, or use the previously specified methodDescription.

Consider `DecoratingMakesSense`, which makes the `doWork` method non-writeable:

```javascript
const DecoratingMakesSense = (object, methodName, description) => {
  console.log('Decorating makes sense')

  description.writable = false
  return description
}

class ExampleWithDetailedDecoration {
  @DecoratingMakesSense
  doWork() {
    console.log('can\'t you see I\'m working here?')
  }
}

const makesSense = new ExampleWithDetailedDecoration()
makesSense.doWork()
makesSense.doWork = () => console.log('some other function')
```

```bash
> node build/main.js
Decorating makes sense
can't you see I'm working here?

/decorator-example/build/main.js:164
makesSense.doWork = function () {
                  ^

TypeError: Cannot assign to read only property 'doWork' of object '#<ExampleWithDetailedDecoration>'
    at Object.defineProperty.value (/decorator-example/build/main.js:164:19)
...
```

### Getting started

Since decorators are currently in the [proposal stage](https://tc39.github.io/proposal-decorators/), getting started requires a little tweaking of your standard babel/webpack/linter configs.

#### Babel

```
> npm install -g webpack
> npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-plugin-transform-decorators-legacy
```

In your .babelrc

```
{
  "presets": [
    "es2015"
  ],
  "plugins": [
    "transform-decorators-legacy"
  ]
}
```

#### Webpack
Here is a barebones webpack.config.js example

```
var path = require('path')

module.exports = {
  entry: './index.js',
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: 'main.js'
  },
  module: {
      loaders: [
    {
      test: /\.js$/,
      exclude: /node_modules/,
      loader: 'babel-loader',
      query: {
        cacheDirectory: true,
        plugins: [
          'transform-decorators-legacy',
        ],
        presets: ['es2015'],
      },
    }
  ]
  },
  stats: {
    colors: true
  }
}
```

(The important bit is to add 'transform-decorators-legacy' to the plugins array.)

#### Linting error -- or is it? 🤔
If you use VSCode, you will probably run across a linter error that says

```
[js] Experimental support for decorators is a feature that is subject to
change in a future release. Set the 'experimentalDecorators' option to
remove this warning.
```

This is an error in the VSCode JS support, rather than a linter error.

Add a jsconfig.json file to your project root with the following contents:

```
{
  "compilerOptions": {
    "experimentalDecorators": true
  }
}
```

Be sure to restart VSCode, and the problem should go away. If it doesn't, follow [this thread](https://github.com/Microsoft/vscode/issues/28097) for further information.

### Build and run

```
> webpack
Hash: 63cf378bd6d165758ed8
Version: webpack 3.8.1
Time: 457ms
  Asset     Size  Chunks             Chunk Names
main.js  4.47 kB       0  [emitted]  main
   [0] ./index.js 1.7 kB {0} [built]
   [1] ./decorator.js 195 bytes {0} [built]

> node build/main.js
Decorating is fun
can't you see I'm working here?
```

For a detailed example checkout [this repo](https://github.com/keighty/decorator--example). For more examples and a deep dive, checkout [wycats's decorator spec](https://github.com/wycats/javascript-decorators)
