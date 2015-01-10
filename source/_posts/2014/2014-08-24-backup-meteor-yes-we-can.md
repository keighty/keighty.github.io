---
layout: post
title: "backup meteor? yes we can"
date: 2014-08-24
comments: true
categories: [meteor, mongo]
---

Retrieving a data dump from your meteor production database and copying it to local development is a three-step, time sensitive process.

###1. Retrieve your production url
Passing a `-url` flag to the `meteor mongo` command will return a long string of goodies:

{% codeblock Dude, where's my data? %}
$ meteor mongo --url my_app.meteor.com

mongodb://client-12345678:9abcdef1-234-5678-9abc-def123456789@singularsensation-db-a3.meteor.io:12345/my_app_meteor_net
{% endcodeblock %}

This string contains all the information you require to access your production data:

* your client id (from // to :)
* a server password (from : to @)
* a server name and port number (from @ to /)
* the datastore identification (from / to the end)

{% raw %}
```bash
mongodb://<CLIENT_ID>:<PASSWORD_HASH>@<SERVER_NAME:PORT>/<YOUR_DATA_STORE>
```
{% endraw %}

Retrieving your data is time-sensitive because the password hash will expire after 60 seconds.

###2. Retrieve your data using mongodump

{% raw %}
```bash
$ mongodump -u CLIENT_ID -h SERVER_NAME:PORT -d YOUR_DATA_STORE -p PASSWORD_HASH

connected to: <SERVER_NAME:PORT>
Sun Aug 24 10:50:32.342 DATABASE: YOUR_DATA_STORE  to   dump/YOUR_DATA_STORE
Sun Aug 24 10:50:32.989   YOUR_DATA_STORE.system.indexes to dump/YOUR_DATA_STORE/system.indexes.bson
Sun Aug 24 10:50:34.368      14 objects
Sun Aug 24 10:50:34.369   YOUR_DATA_STORE.system.users to dump/YOUR_DATA_STORE/system.users.bson
Sun Aug 24 10:50:34.559      2 objects
... and on and on
```
{% endraw %}

Mongodb copies your data from production into a new folder called "dump" in your present working directory.

###3. Use mongorestore to copy the data from the dump into your local datastore
{% codeblock %}
$ mongorestore --host 127.0.0.1 --port 3001 --db meteor --drop dump/YOUR_DATA_STORE/

connected to: 127.0.0.1:3001
Sun Aug 24 11:36:54.693 dump/YOUR_DATA_STORE/users.bson
Sun Aug 24 11:36:54.693   going into namespace [meteor.users]
Sun Aug 24 11:36:54.693    dropping
2 objects found
Sun Aug 24 11:36:55.834   Creating index: { name: "_id_", key: { _id: 1 }, ns: "meteor.users" }
Sun Aug 24 11:36:55.993   Creating index: { name: "username_1", key: { username: 1 }, unique: true, ns: "meteor.users", sparse: 1 }
... and on and on
{% endcodeblock %}

Awesome.

Resources:
[stackoverflow](http://stackoverflow.com/a/12447710)