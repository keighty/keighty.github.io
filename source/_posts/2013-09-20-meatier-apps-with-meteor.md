---
layout: post
title: "Meatier Apps With Meteor"
comments: true
description: ""
category: javascript
tags: []
---

[Meteor](http://www.meteor.com/) is a framework that allows you to write web applications using only javascript. All the work is done on the client-side. Meteor embraces new principles of web design: data on the wire, one language is enough, and simplicity makes for productivity.

Simplicity is something I value as well, and while setting up meteor on my linux machine was not a three-step process as outlined in their examples, it only took a little tinkering to figure out the problem. <!--more-->First, a look at the three meteor setup steps:

####Install
{% highlight bash %}
$ curl https://install.meteor.com | /bin/sh {% endhighlight %}

####Create a sample application
{% highlight bash %}
$ meteor create testapp {% endhighlight %}

####Run the sample application
{% highlight bash %}
$ cd testapp
$ meteor
{% endhighlight %}

This is where the meteor server is supposed to start, connecting to your mongo installation, and serving your application on port 3000:
{% highlight bash %}
$ meteor
[[[[[ /path/to/testapp ]]]]]

=> Meteor server running on: http://localhost:3000/
{% endhighlight %}

Instead, my terminal became quite upset about it:
{% highlight bash %}
[[[[[ /path/to/testapp ]]]]]

Unexpected mongo exit code 1. Restarting.
Unexpected mongo exit code 1. Restarting.
Unexpected mongo exit code 1. Restarting.
Can't start mongod {% endhighlight %}

Whups! Everything seems normal in my mongo installation, but after a lot of searching I finally found the problem on [stackoverflow](http://stackoverflow.com/questions/18505372/meteor-update-0-6-4-0-6-5-mongo-error). From among the answers I found this solution:

>I'm also getting this error on Ubuntu. As mentioned, it's caused by mongo and mongod from ~/.meteor/tools/latest/mongodb/bin being compiled with an older version of glib. You can replace the version of mongo bundled by meteor with the version installed in your system:
{% highlight bash %}
cd ~/.meteor/tools/latest/mongodb/bin/
mv mongo mongo-backup
mv mongod mongod-backup
ln -s /usr/bin/mongo
ln -s /usr/bin/mongod {% endhighlight %}

Brilliant! Following these instructions, I was finally able to start meteor.

###Getting down to business
Now to test out some of the awesome functionality of this framework. I [followed an example](http://www.meteor.com/examples/todos) and created a todo list:

{% highlight bash %}
$ meteor create --example todos
$ cd todos
$ meteor deploy totally-fake-url.meteor.com
Deploying to totally-fake-url.meteor.com.  Bundling...
Uploading...
Now serving at totally-fake-url.meteor.com {% endhighlight %}

Playing around with creating new todos and lists, it is clear that data is being added and changed instantly and persistently.

A Meteor application is a mix of JavaScript that runs inside a client web browser, JavaScript that runs on the Meteor server inside a Node.js container, and all the supporting HTML fragments, CSS rules, and static assets.

I am really looking forward to diving further into [the docs](http://docs.meteor.com/#concepts) to learn more about this incredible framework.
AWESOME