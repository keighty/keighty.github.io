---
layout: post
title: "mongo trouble? reset!"
date: 2014-05-03
comments: true
categories: [javascript, meteor, ubuntu wrestling]
---

Back in March I wrote about letting my computer's battery die in the middle of a meteor project, with the consequence being that I could not connect to the underlying mongo database:
{% codeblock What's wrong, meteor? lang:bash %}
$ meteor
[[[[[ ~/project/in/progress ]]]]]

=> Started proxy.
Unexpected mongo exit code 100. Restarting.
Unexpected mongo exit code 100. Restarting.
Unexpected mongo exit code 100. Restarting.
Can't start Mongo server.
MongoDB had an unspecified uncaught exception.
This can be caused by MongoDB being unable to write to a local database.
Check that you have permissions to write to .meteor/local. MongoDB does
not support filesystems like NFS that do not allow file locking.
{% endcodeblock %}

After much spelunking in stackoverflow and documentation, I discovered the magic of this command:
{% codeblock Magic mongo reset spell lang:bash %%}
$ sudo -u mongodb mongdo --repair --dbpath /var/lib/mongodb
$ sudo service mongodb start
{% endcodeblock %}

Repairing and restarting the mongo service seemed to do the trick, and I continued on my merry meteor way. Well, I did it again -- I let my battery die in the middle of a meteor project, and with feelings of smug satisfaction, I plumbed my blog archive for the answer. Only this time the magic antidote didn't work<!--more-->:

{% codeblock This log has been redacted to protect the ignorant %}
$ sudo -u mongodb mongod --repair --dbpath /var/lib/mongodb/
[sudo] password for keighty:
Sat May  3 09:52:04.793 [initandlisten] finished checking dbs
Sat May  3 09:52:04.793 dbexit:
Sat May  3 09:52:04.793 [initandlisten] shutdown: going to close listening sockets...
Sat May  3 09:52:04.793 [initandlisten] shutdown: going to flush diaglog...
Sat May  3 09:52:04.793 [initandlisten] shutdown: going to close sockets...
Sat May  3 09:52:04.793 [initandlisten] shutdown: waiting for fs preallocator...
Sat May  3 09:52:04.793 [initandlisten] shutdown: closing all files...
Sat May  3 09:52:04.793 [initandlisten] closeAllFiles() finished
Sat May  3 09:52:04.793 [initandlisten] shutdown: removing fs lock...
Sat May  3 09:52:04.794 dbexit: really exiting now

$ sudo service mongodb start
mongodb start/running, process 2415
$ meteor
[[[[[ ~/project/in/progress ]]]]]

=> Started proxy.
Unexpected mongo exit code 100. Restarting.
Unexpected mongo exit code 100. Restarting.
Unexpected mongo exit code 100. Restarting.
Can't start Mongo server.
MongoDB had an unspecified uncaught exception.
This can be caused by MongoDB being unable to write to a local database.
Check that you have permissions to write to .meteor/local. MongoDB does
not support filesystems like NFS that do not allow file locking.
{% endcodeblock %}

C'mon -- It worked the last time! I tried it again with the same result. I tried `chown`-ing the local db. I tried `chown`-ing the database in `/var/lib/mongodb`. After each attempted solution the mongobd service would start, but meteor hiccuped on writing to the database. So I tried something radical. Something that wasn't in any of the manuals, or any of the [responses to similar questions](http://stackoverflow.com/questions/5798549/why-cant-i-start-the-mongodb) on stackoverflow:

{% codeblock Really? That was all? lang:bash %}
$ meteor reset
Project reset.
$ meteor
[[[[[ ~/project/in/progress ]]]]]

=> Started proxy.
=> Started MongoDB.
=> Started your app.

=> App running at: http://localhost:3000/
{% endcodeblock %}

Turns out that if you hard shut-down your computer while your meteor project's mongo instance is running, it remains in a locked mode. Since it was only a development database I took a chance that dropping it would cure my ills. Voila! Meteor and mongodb are friends again.

Awesome.