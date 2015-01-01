---
layout: post
title: "mongod maintenance"
date: 2014-01-22 07:23:10 -0800
comments: true
categories: mongodb
---

My laptop's battery died, and I let it. Not a big deal, it is an old laptop and is used to being abused. Every time it does a hard shut down like that I wonder if it will recover. The laptop recovered just fine, but mongo did not. When my computer coughed back to life, I politely requested a meteor server, to which meteor replied that it could not start my server because it could not find a running mongodb. My first instinct, naturally, was to start it up again<!--more-->:

```bash
$ mongo start
MongoDB shell version: 2.4.6
connecting to: start
Mon Jan 20 07:42:47.289 Error: couldn't connect to server 127.0.0.1:27017 at src/mongo/shell/mongo.js:145
exception: connect failed
```

Followed by a trip to the logs:
```bash This error has been reformatted to fit your tv.
$ tail /var/log/mongodb/mongodb.log
*************
[initandlisten] exception in initAndListen: 10309 Unable to create/open lock file: /var/lib/mongodb/mongod.lock errno:13 Permission denied Is a mongod instance already running?, terminating
dbexit:
[initandlisten] shutdown: going to close listening sockets...
[initandlisten] shutdown: going to flush diaglog...
[initandlisten] shutdown: going to close sockets...
[initandlisten] shutdown: waiting for fs preallocator...
[initandlisten] shutdown: closing all files...
[initandlisten] closeAllFiles() finished
[initandlisten] shutdown: removing fs lock...
[initandlisten] couldn't remove fs lock errno:9 Bad file descriptor
dbexit: really exiting now
```

A lock file, you say? Well! I know what to do with those!

```bash Are you sure you want to mess with this?
$ rm /var/lib/mongodb/mongod.lock
rm: remove write-protected regular file `/var/lib/mongodb/mongod.lock'? n
```

No, I am not sure -- thanks for asking! After a deep breath and a reminder that I should not be so hasty (rtfm, katie), I found this note on [mongodb.org](http://docs.mongodb.org/manual/reference/configuration-options/#repair):

>Note: Because mongod rewrites all of the database files during the repair routine, if you do not run repair under the same user account as mongod usually runs, you will need to run chown on your database files to correct the permissions before starting mongod again.

A file rewrite will include a lock file, will it not?

```bash
$ sudo -u mongodb mongod --repair --dbpath /var/lib/mongodb/
[sudo] password for keighty:
...
sudo service mongodb start

```

Awesome.