---
layout: post
title: "least recently used (LRU) cache"
date: 2018-02-23
sharing: true
categories: [javascript, algorithms]
---

I was recently challenged to implement a least recently used (LRU) cache in javascript, which taxed both my object-oriented javascripting chops as well as my hazy memory of how to implement a linked list.<!--more--> An LRU cache discards least recently used items first ([wikipedia](https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_Recently_Used_(LRU))). Not only do you have to limit the size of the list, but you have to track when an item has been accessed recently and ensure it is not removed before an item that has been accessed less recently.

Judging from the scores of implementations available from my first Google search, this appears to be a fairly common interview question. Here is the approach that I took:

1. Maintain performance by storing items in a hash map
2. Use a queue-type linked list to keep track of access order: a new item is added to the end of the queue, while an existing item is removed from its place in the queue and re-added to the end of the queue.
3. Keep the cache at the specified size by lopping off the head of the list (and removing those items from the map).
4. Do it all using object oriented design principles.

### The API/object design

A simple cache should not have too many bells and whistles. It needs a `get` and a `set`, and possbily a `size` (useful for testing, probably not something most users desperately need). It should take a specific `cacheSize` as well, so we can set limits on the cache at startup.

```javascript
const LRU = function (cacheSize) {
  this._maxSize = cacheSize

  this.get = (key) => {}
  this.set = (key, value) => {}
  this.size = () => {}
}
```

### The data structures

To access stored values in the cache in the most performant way, we store them with a key in a hash map:

```javascript
// ...
this._map = new Map()
// ...
```

In order to keep track of order access, we need to keep references to the head and the tail of the list, as well as an object to store the cached value and its place in the queue:

```javascript
//...
this._head = null
this._tail = null
//...

const Node = function(key, value) {
  this.key = key;
  this.value = value;
  this.next = null;
  this.prev = null;
};
```

### The implementaton

```javascript
const LRU = function(cacheSize) {
  this._maxSize = cacheSize;
  this._map = new Map();
  this._head = null;
  this._tail = null;

  this.size = () => this._map.size;

  this.get = key => {
    const item = this._map.get(key);

    if (item) {
      this._dequeue(item);
      this._enqueue(item);
      return item.value;
    }
    return;
  };

  this.set = (key, value) => {
    const node = new Node(key, value);

    if (this._map.has(key)) {
      const existingNode = this._map.get(key);
      this._dequeue(existingNode);
    }

    this._enqueue(node);
    this._trimList();

    return value;
  };

  this._trimList = () => {
    if (this._map.size > this._maxSize) {
      this._dequeue(this._head);
    }
  };

  this._enqueue = node => {
    this._map.set(node.key, node);
    if (!this._head) {
      this._head = node;
      this._tail = node;
    } else {
      this._tail.next = node;
      node.prev = this._tail;
      node.next = null;
      this._tail = node;
    }
    return node.value;
  };

  this._dequeue = node => {
    if (this._head === node) {
      const { next, key } = this._head;
      if (next) {
        this._head = next;
        this._head.prev = null;
      } else {
        this._head = null;
        this._tail = null;
      }
    } else {
      const { prev, next } = node;
      prev.next = next;
      next.prev = prev;
    }
    this._map.delete(node.key);
  };

  this.toString = () => {
    const itr = this._map.entries();
    const entries = [];
    for (let item of itr) {
      const [key, node] = item;
      const { value } = node;
      entries.push([key, value]);
    }
    return entries;
  };
};

const Node = function(key, value) {
  this.key = key;
  this.value = value;
};

module.exports = LRU;
```

Checkout [this repo](https://github.com/keighty/lru-cache) for the full implementation and tests.
