---
layout: post
title: "meteor, fetch!"
date: 2014-03-31
comments: true
categories: [meteor, javascript]
---

From [Discover Meteor](http://book.discovermeteor.com/): find() returns a cursor, which is a reactive data source.

Data from reactive sources will be updated automatically to track changes in the data used to generate it. <!--more-->Cursors are derived from specific sources:

* Session variables
* Database queries on Collections
* Meteor.status
* The ready() method on a subscription handle
* Meteor.user
* Meteor.userId
* Meteor.loggingIn

{% codeblock For example, find() using a database query lang:js %}
&gt; Posts.find()
LocalCollection.Cursor {collection: LocalCollection, sorter: null, _selectorId: undefined, matcher: Minimongo.Matcher, skip: undefined…}
      _selectorId: undefined
      _transform: null
      collection: LocalCollection
      cursor_pos: 0
      db_objects: null
      fields: undefined
      limit: undefined
      matcher: Minimongo.Matcher
      reactive: true
      skip: undefined
      sorter: null
      __proto__: Object
{% endcodeblock %}

Cursors are not query snapshots. They are query snapshot containers -- the contents of which are updated by changes to the underlying data. To interact with the data contained in a cursor, we can use fetch() to transform the cursor into an array.

{% codeblock A cursor becomes an array with find().fetch() lang:js %}
&gt; Posts.find().fetch()
[
    Object
    _id: "z4z36DFCaGr8Ptuqd"
    title: "The Way We Live Now"
    author: "Anthony Trollope"
    __proto__: Object
    ...
]
{% endcodeblock %}

Meteor can iterate over a cursor without first converting it to an array, but in order to interact with the data directly, you must first use fetch(), map(), or forEach(). For more cursor fun, checkout the [Meteor docs](http://docs.meteor.com/#reactivity).

Awesome.