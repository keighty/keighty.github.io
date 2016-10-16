---
layout: post
title: "The EventEmitter Pattern and the Event Loop -- a timeline"
date: 2016-10-14
sharing: true
categories: [javascript, node]
---

The EventEmitter pattern is a derivative of the Observer pattern: an object can notify a set of observers when a change in its state occurs.<!--more--> Consider the following example using the EventEmitter pattern:

```javascript
const EventEmitter = require('events').EventEmitter
const fs = require('fs')

const findPattern = (files, regex) => {
  const emitter = new EventEmitter()

  files.forEach(file => {
    fs.readFile(file, 'utf8', (err, content) => {
      if (err) return emitter.emit('error', err)

      emitter.emit('fileread', file)

      let match
      if (match = content.match(regex)) {
        match.forEach(elem => {
          emitter.emit('found', file, elem)
        })
      }
    })
  })
  return emitter
}

// fileA.txt contains the words "hello blah blah blah"
// fileB.json is empty
// fileC.md does not exist
const files = ['fileA.txt', 'fileB.json', 'fileC.md']

findPattern(files, /hello \w+/g)
  .on('fileread', file => {console.log(`${file} was read`)})
  .on('found', (file, match) => {console.log(`Matched ${match} in file ${file}`)})
  .on('error', err => {console.log(`Error emitted: ${err.message}`)})
```

Some of the script is executed synchronously, but the real work is done asynchronously. The EventEmitter pattern takes advantage of the event loop to work efficiently.

Keep the following schematic in mind as you parse through the execution of this script:

{% img /images/161012-callbacks/event-loop.png %}

_image credit: Mario Casciaro and Luciano Mammino from Node.js Design Patterns. Colour notations are mine._


The first set of operations happen synchronously:

* call `findPattern` with the file list and regex pattern
* create a new event emitter
* submit `readFile` I/O request for fileA.txt to the event demultiplexer
* submit `readFile` I/O request for fileB.json to the event demultiplexer
* submit `readFile` I/O request for fileC.md to the event demultiplexer
* return the event emitter
* register `fileread` listener on the event emitter
* register `found` listener on the event emitter
* register `error` listener on the event emitter

{% img /images/161014-event-emitter/event-emitter-1.png %}

Once the I/O operations are submitted to the event demultiplexer, they will return in the order in which the requests are fulfilled. In this example, fileC.md does not exist, so the event demultiplexer submits the result along with the handler to the Event loop.

* The event loop runs the result through the callback for fileC.md, which **emits an error event** (line 9)! The EventEmitter adds the error handler to the event loop.

{% img /images/161014-event-emitter/event-emitter-2.png %}

In the meantime, the event demultiplexer has fulfilled the I/O request for fileB.json, and added the result and the callback to the event loop.

* The event loop runs the result through the callback for fileB, which emits a `fileread` event (line 11). The EventEmitter adds the `fileread` handler to the event loop.

The event demultiplexer also fulfills the I/O request for fileA.txt and adds the result and the callback to the event loop.

* The event loop runs the result through the callback for fileA, which emits a `fileread` event (line 11) AND a `found` event (line 16). The EventEmitter adds the `fileread` and `found` handlers to the event loop.

The event loop is busily processing all of these events in the order of arrival:

* run the listener callback for the `error` event (fileC)
* run the listener callback for the `fileread` event (fileB)
* run the listener callback for the `fileread` event (fileA)
* FINALLY -- run the listener callback for the `found` event (fileA)

The result of running this script on the command line:

```text
$ node --use_strict events.js
Error emitted: ENOENT: no such file or directory, open 'fileC.md'
fileB.json was read
fileA.txt was read
Matched hello blah in file fileA.txt
```

_Side note: set the flag `--use-strict` to run scripts using ES6 syntax_

Why did the files process in what seems to be reverse order? The event demultiplexer adds work to the event loop as it fulfills I/O requests. When `readfile()` is given three files -- a file with content (fileA), an empty file (fileB), and a non-existent file (fileC) -- it is reasonable to assume that the I/O request for a non-existent file will return first, as there is no file to read. It is also reasonable that an empty file will  return next, as there is very little work to be done reading an empty file. Finally, the I/O request for the file with content returns -- it requires more work to read a file with content than an empty file.

For more about the event emitter pattern in Node, check out [Node.js Design Patterns](https://www.amazon.com/Node-js-Design-Patterns-Mario-Casciaro/dp/1785885588/ref=sr_1_1/161-7210115-5247461?ie=UTF8&qid=1476284148&sr=8-1&keywords=node.js+design+patterns) by Mario Casciaro and Luciano Mammino.
