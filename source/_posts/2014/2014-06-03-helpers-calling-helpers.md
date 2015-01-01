---
layout: post
title: "helpers calling helpers"
date: 2014-06-03
comments: true
categories: [meteor, javascript]
---

Whittling away at the Discover Meteor tutorial for creating a social news sharing site, I was implementing a Comments collection and related accoutrements (templates, helpers, forms, etc.) when I uncovered a need to call one helper method from another<!--more-->.

I was using this handy method to return the number of comments made on a particular post:
{% codeblock Helpful helpers lang:js %}
Template.postPageItem.helpers({
  commentsCount: function() {
    return Comments.find({postId: this._id}).count();
  }
});
{% endcodeblock %}

{% codeblock Template helper magic sauce lang:js %}
{% raw %}
submitted by {{postAuthor}}, with
<a href="{{pathFor 'postPage'}}">{{commentsCount}} comments</a>
{% endraw %}
{% endcodeblock %}

Which returned a very ordinary:
> submitted by meImAnAwesomeUser, with 1 comments

1 **comments**?! That makes no sense.. I didn't minor in English so I could let that travesty fly. I made a new helper method:

{% codeblock This WORKS! lang:js %}
Template.postPageItem.helpers({
  commentsCount: function() {
    return Comments.find({postId: this._id}).count();
  },
  commentsNote: function() {
    return Comments.find({postId: this._id}).count() === 1 ? "comment" : "comments";
  }
});
{% endcodeblock %}

UGH! What is that on line 6? Possibly the ugliest ternary statement I have written today (probably). Is that code duplication I smell? Won't reusing `commentsCount` remove the redundancy?

{% codeblock Starting to DRY the meteor lang:js %}
Template.postPageItem.helpers({
  commentsCount: function() {
    return Comments.find({postId: this._id}).count();
  },
  commentsNote: function() {
    return commentsCount === 1 ? "comment" : "comments";
  }
});
{% endcodeblock %}

Only.. that doesn't work:

    Exception from Deps recompute function: ReferenceError: commentsCount is not defined
    at Object.Template.postPageItem.helpers.commentsNote (http://localhost:3000/client/views/posts/post_page_item.js?160ec9a926881c795377a47c15affc4f553c4d88:9:12)



But it is RIGHT ABOVE! I can see it! It is in the same file! I tried all permutations of `commentsCount()`, `this.commentsCount`, `self.commentsCount`, `this.self.commentsCount()`, and they all threw either similar errors or `undefined`. Calling the prototype method directly turns out to work just fine:

{% codeblock THIS is not THAT and also doesn't work lang:js %}
Template.postPageItem.helpers({
  commentsCount: function() {
    return Comments.find({postId: this._id}).count();
  },
  commentsNote: function() {
    var count = Template.postPageItem.commentsCount()
    return count === 1 ? "comment" : "comments";
  }
});
{% endcodeblock %}

Except that when `commentsCount` is called on line 6, `this` is no longer the lovely Post item provided by the template. No, `this` becomes the context of the `commentsNote` function, and therefore calling `this._id` will return `undefined`. The closure issue can be worked around with a little argument passing:

{% codeblock Is DRY so much better? lang:js %}
Template.postPageItem.helpers({
  commentsCount: function(id) {
    var count_id = typeof id !== 'undefined' ? id : this._id;
    return Comments.find({postId: count_id}).count();
  },
  commentsNote: function() {
    var count = Template.postPageItem.commentsCount(this._id)
    return count === 1 ? "comment" : "comments";
  }
});
{% endcodeblock %}

Wow. Passing in the id works, but now I have to check the context to determine the appropriate id. That is the most unreadable-but-reused code I have written today (probably), and I learned some valuable lessons on this frantic search for DRY:

* `self`, whether called from a helper or the console, will always be `window`
* `this` called from a Template helper refers to the item of the collection that is passed in to the template
* because helper methods are closures, `this` in the calling method is different from `this` in the called method
* don't dry out your code to the point of unreadability

In the end, cleaning up the ugly ternary makes the code reuse more reasonable.

{% codeblock This is just fine lang:js %}
Template.postPageItem.helpers({
  commentsCount: function() {
    return Comments.find({postId: this._id}).count();
  },
  commentsNote: function() {
    var count = Comments.find({postId: this._id}).count();
    return count === 1 ? "comment" : "comments";
  }
});
{% endcodeblock %}

Awesome.