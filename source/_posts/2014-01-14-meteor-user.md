---
layout: post
title: "Meteor user"
date: 2014-01-14 19:38:16 -0800
comments: true
categories: javascript
---

["Getting Started with Meteor.js JavaScript Framework"](http://www.packtpub.com/getting-started-with-meteor-javascript-framework/book) by Isaac Strack is a great introduction to a powerful tool.
<!--more-->
### User accounts in three easy steps
Meteor rolls its own user accounts system, and getting it running on your application is a three step process:

{% raw %}
```bash 1) command line: Add the relevant packages
$ meteor add accounts-base
accounts-base: A user account system
$ meteor add accounts-password
accounts-password: Password support for accounts
$ meteor add email
email: Send email messages
$ meteor add accounts-ui
```
{% endraw %}

{% raw %}
```html 2) html: Add a div to hold the login button
<div style="float:right; margin-right:20px;">
  {{loginButtons align="right"}}
</div>
```
{% endraw %}


```javascript 3) client javascript: Add an Accounts config
Accounts.ui.config({
  passwordSignupFields: 'USERNAME_AND_OPTIONAL_EMAIL'
});
```

##The catch
While the server side and client side code looks similar they are definitely not interchangeable. Server code run on the client side will likely fail silently, but client code run on the server will crash the app. One place where I got tripped up was accessing the userId:

###Server side ```this.userId```
{% pullquote %}
In Meteor server side javascript the context is clear: ```this``` is called in the context of a Meteor object, and you can therefore make inquiries about its properties. {"Use this.userId inside the server publish function"} to access the id of the current user (or null if no user is logged in -- from the [docs](http://docs.meteor.com/#publish_userId)).
{% endpullquote %}

###Client side: ```Meteor.userId()```
{% pullquote %}
In the Meteor client side, ```this``` has a different context: the ```window``` object. The window object has a userId property that can be set at Document.ready(), but in the case that a user logs out and another user logs in to that same window without a page refresh, ```window.userId``` will retain the id of the first user. {"Use Meteor.userId() anywhere BUT in the server publish function"} to get the current user id (or null if no user is logged in -- from the [docs](http://docs.meteor.com/#accounts_api)).
{% endpullquote %}

Thanks to this [stackoverflow](http://stackoverflow.com/a/20781135) response, the Meteor [docs](http://docs.meteor.com/), and packtpub's awesome [support page](http://www.packtpub.com/getting-started-with-meteor-javascript-framework/book) for helping me get reacquainted with javascript contexts.

Awesome
