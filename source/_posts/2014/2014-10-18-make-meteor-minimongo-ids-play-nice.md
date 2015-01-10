---
layout: post
title: "meteor-managed ids won't play nicely with others"
date: 2014-10-18
comments: true
categories: [node, mongo, meteor]
---

I am working with a data dump from a meteor project, and while retrieving and displaying the existing collection was no problem at all, I was stuck on saving new documents to mongo.

My schema was pretty straightforward and taken directly from an existing document:

{% codeblock %}
var postSchema = mongoose.Schema({
  "title" : String,
  "company" : String,
  "author" : String,
  "music" : String,
  "choreographer" : String,
  "showDate" : String,
  "image": String,
  "userId" : String,
  "postAuthor" : String,
  "submitted" : Number,
  "commentsCount" : Number,
  "_id":  String
});
{% endcodeblock %}

The problem was that last field: `_id`. If I included it in the schema then I would have the objectId available on the front end but would get an error trying to insert a new document: `[Error: document must have an _id before saving]`. If I removed `_id` from the schema I could save documents just fine but could not pass through the objectIds of already existing documents. I have learned that mongo is very flexible when it comes to assigning ids before insertion into the database.

Mongo objects created through a meteor application are given meteor-friendly objectIds, the uniqueness of which is monitored and maintained by a meteor wrapper class. The design decision to go with ids as strings seems to be [motivated by meteor's latency compensation feature](https://groups.google.com/forum/#!topic/meteor-talk/f-ljBdZOwPk
) -- creating documents on the client-side and then later syncing them with the server requires that they are assigned objectIds before they ever reach mongo. Mongo will accept a manually entered id, trusting that there is an entity somewhere that is managing uniqueness.

Here was my problem: the documents created by meteor had String ids assigned by meteor. If I included `_id` in my schema, mongo would assume that I was managing id assignment manually, and wouldn't save the object until I did. If I did not include `_id` in my schema, mongo would assume it was responsible, and would assign a unique id accordingly.

To make the documents created by meteor play nice with the new stack, I had to update each document's `_id` with one created by mongo. Unfortunately, mongo won't allow modification of `_id` directly, so the only choice was to recreate and insert each entry, then delete the original:

1. Retrieve a document: `doc = db.posts.findOne()`
2. Save the title: `title = doc.title`
3. Save the document's string id: `id = doc._id`
4. Reassign the document id: `doc._id = ObjectId()`
5. Save the updated document: `db.posts.insert(doc)`
6. Remove the original document: `db.posts.remove({_id: id})`
7. Verify that the post exists with the new id: `db.posts.findOne({"title": title})`

{% raw %}
```
> doc = db.posts.findOne()
{
  "title" : "La Cage aux Folles",
  "company" : "Pixie Dust Productions",
  "author" : "Harvey Fierstein",
  "music" : "Jerry Herman",
  "showDate" : ISODate("2014-09-21T07:00:00Z"),
  "dateSubmitted" : 1411346259065,
  "_id" : "EjzopWQa3mw5LB979"
}

> title = doc.title
La Cage aux Folles

> id = doc._id
EjzopWQa3mw5LB979

> doc._id = ObjectId()
ObjectId("5442af27cc37fe6f03648fdd")

> db.posts.insert(doc)

> db.posts.remove({_id: id})

> db.posts.findOne({"title": title})
{
  "_id" : ObjectId("5442af27cc37fe6f03648fdd"),
  "title" : "La Cage aux Folles",
  "company" : "Pixie Dust Productions",
  "author" : "Harvey Fierstein",
  "music" : "Jerry Herman",
  "showDate" : ISODate("2014-09-21T07:00:00Z"),
  "dateSubmitted" : 1411346259065,
  "commentsCount" : 1
}
>

```
{% endraw %}

Here is a handy script for changing the entire collection at once:

```
db.posts.find().forEach(function(doc){ var doc = db.posts.findOne(); var title = doc.title; var id = doc._id; doc._id = ObjectId(); db.posts.insert(doc); db.posts.remove({_id: id}); })
```

Awesome.
