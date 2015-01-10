---
layout: post
title: "Sort all the things with mongo and node.js"
date: 2014-10-09
comments: true
categories: [node, mongo, angular]
---

Sorting shouldn't be difficult, but through my experience learning meteor I discovered that if mongo likes sort commands one way:

`db.posts.find().sort({submitted: -1})`

meteor would like them a different way:

`Posts.find({}, {sort: {submitted : -1}}`

I am adapting one of my meteor projects to use mongo and node.js, connected through mongoose, and fitting in the callback required for node sent me to the google. After a few stackoverflow searches I turned up a several options:

{% codeblock Option 1 (spoiler.. it didn't work) %}
Post.find({}, {sort:[['submitted',-1]]}, function(err, doc) {
  response.send(doc);
});
{% endcodeblock %}

This option returned a object containing only the entry ids, not the full documents:

{% raw %}
```
0: {_id:2tcte4Srd8QMSqrKt}
1: {_id:5c3FdWphiYiMQzukw}
2: {_id:62vAt5ewucKm542DH}
3: {_id:AWNYsxBgFnv9Z4NSA}
...
```
{% endraw %}

Another option suggested that converting the result to an Array would complete the query:

{% codeblock Option2 (spoiler.. also didn't work) %}
Post.find({},{sort: [['submitted',-1]]}).toArray(function(e, results){
  response.send(results);
});
{% endcodeblock %}

This option resulted in `TypeError: Object #<Query> has no method 'toArray'`.

Rather than continuing to search for a snippet to steal, I turned to the source, and it turns out that querying mongo from mongoose is simpler than either of these strategies.

{% codeblock Option3 -- just right %}
Post.find()
  .sort("-submitted")
  .exec(function(err, doc) {
    response.send(doc);
  });
{% endcodeblock %}

Success! Why? Because while mongo itself returns a cursor object which can be transformed into documents using `toArray()`, or `fetch()` in the case of meteor, mongoose returns a [Query](http://mongoosejs.com/docs/queries.html) object, which will return the full documents once it is passed a callback.

A Query object can be built using [method chaining](https://github.com/LearnBoost/mongoose/blob/master/lib/query.js#L211-L216) --
 each method (find | where | limit | select | sort) returns a new Query object, which allows you to build in stages:

{% codeblock %}
Post
.find({ title: /the/ }) // query
.where('cost').gt(17).lt(66)  // query
.where('location').in(['Schnitz', 'Armory'])  // query
.limit(10)  // query
.sort('-ticketDate')  // query
.select('title company')  // query
.exec(callback);  // EXECUTE THE QUERY
{% endcodeblock %}

Another fun bit of javascript/mongoose magic, is that you can indicate [sorting in descending order](https://github.com/LearnBoost/mongoose/blob/master/lib/query.js#L1295-L1297
) by prefacing the sort string with a "-" :

{% codeblock %}
Post
.find({ title: /the/ }) // query
.sort('-ticketDate')  // DESC!
.exec(callback);  // EXECUTE THE QUERY
{% endcodeblock %}

Awesome.

### Resources
[mongo docs](http://docs.mongodb.org/manual/reference/operator/meta/orderby/)

[stackoverflow](http://stackoverflow.com/a/20859457)
