---
layout: post
title: "nested routes with expressjs"
date: 2016-04-12
sharing: true
categories: [expressjs, javascript]
---

Whenever I need to build a quick web application I turn to [ExpressJS](http://expressjs.com/). It is a fast, minimal, easy to configure web server that puts the **E** in [MEAN](http://mean.io/#!/)). I wanted to build a REST API for a hobby app and found that the docs for how to nest routes are relatively few (see [blog](http://codetunnel.io/an-intuitive-way-to-organize-your-expressjs-routes/), [stackoverflow answer](http://stackoverflow.com/a/25305272/1279340)). Combining these two resources, I learned a simple method for keeping your routes separate while creating a nested routing structure. <!--more-->

## The Plan
I want to create routes that look like these:

* `/foo`
* `/foo/bar`
* `/foo/42`
* `/foo/42/baz`
* `/foo/42/baz/123`

Using the [express-generator](http://expressjs.com/en/starter/generator.html) and a few simple commands, you can get your server up and running fast:

```
$ npm install express-generator -g
$ express myapp
$ cd myapp
$ npm install
$ npm start
```

You will end up with a working directory that looks like this:

```
├── app.js
├── bin
│   └── www
├── node_modules
│   ├── ...
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.jade
    ├── index.jade
    └── layout.jade
```

Go ahead and delete the `/routes/users.js` file and the following lines from `app.js`

```javascript app.js
...
var users = require('./routes/users');
...
app.use('/users', users);
```

Then create a new routes file named `/routes/foo.js` and and require it from your app server.
```javascript app.js
...
app.use('/foo', require('./routes/foo'));
...
```

## How to `GET /foo`
Inside `/routes/foo.js`, you need to require the router module from ExpressJS, define a route on it, and export it back out.

```javascript /routes/foo.js
var express = require('express')
var router = express.Router()

// GET /foo
router.get('/', function (req, res, next) {
  res.send('this is the index for foo')
})

module.exports = router
```

Everytime you change a route, you need to restart your server. Nevigating to [localhost:3000/foo](http://localhost:3000/foo) should display the message:

{% img /images/160412-nested-routes/foo.png %}

## How to `GET /foo/bar`
Create simple nested routes by requiring the child route file from the parent route file.

```javascript /routes/foo.js
var express = require('express')
var router = express.Router()
var bar = require('./bar')

// GET /foo
router.get('/', function (req, res, next) {
  res.send('this is the index for foo')
});

// GET /foo/bar
router.use('/bar', bar) // tell the router to use bar.js for child routes

module.exports = router
```

Create a new routes file named `/routes/bar.js` and define a root route the same way you did for `/routes/foo.js`:

```javascript /routes/bar.js
var express = require('express')
var router = express.Router()

// GET /foo/bar
router.get('/', function (req, res, next) {
  res.send('this is the index for bar')
});

module.exports = router
```

Bounce your server again and navigate to [localhost:3000/foo/bar](http://localhost:3000/foo/bar)

{% img /images/160412-nested-routes/foobar.png %}

## How to `GET /foo/42`
Expecting a parameter? No problem! access URL params directly from the request object: `req.params.nameOfParam`:

```javascript /routes/foo.js
...
// GET /foo/42
router.get('/:number', function (req, res, next) {
  res.send('this is foo #' + req.params.number)
})

module.exports = router
```
Navigate to [localhost:3000/foo/42](http://localhost:3000/foo/42) to see the result.

{% img /images/160412-nested-routes/fooNumber.png %}

## How to `GET /foo/42/baz`
Getting a child route from a parameterized parent is where I was getting confused. It turns out that you need to configure the router a little differently when you are passing params through.

```javascript /routes/foo.js
...
var baz = require('./baz')

...
// GET /foo/42/baz
router.use('/:number/baz', baz)

module.exports = router
```

You need to pass an options object to `express.Router` to merge the params from any parent route:

```javascript /routes/baz.js
var express = require('express')
var router = express.Router({mergeParams: true}) // don't forget the parent params!

// GET /foo/42/baz
router.get('/', function (req, res, next) {
  // the param name is from the parent as well
  res.send('this is the baz for foo#' + req.params.number);
})

module.exports = router
```

Navigate to [localhost:3000/foo/42/baz](http://localhost:3000/foo/42/baz) to enjoy your success.

{% img /images/160412-nested-routes/fooNumberBaz.png %}

## How to `GET /foo/42/baz/123`
Parameterizing a child route is the same process as for the parent route:

```javascript /routes/baz.js
var express = require('express')
var router = express.Router({mergeParams: true})

// GET /foo/42/baz
router.get('/', function (req, res, next) {
  res.send('this is the baz for foo#' + req.params.number);
})

// GET /foo/42/baz/123
router.get('/:id', function (req, res, next) {
  res.send('baz #' + req.params.id + ‘ for foo #’ + req.params.number)
})

module.exports = router
```

Navigate to [localhost:3000/foo/42/baz/123](http://localhost:3000/foo/42/baz/123) to enjoy your success.

{% img /images/160412-nested-routes/fooNumberBazNumber.png %}

TADA! a model of nested routes that you can apply to any application.

