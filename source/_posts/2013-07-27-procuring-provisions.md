---
layout: post
title: "Procuring Provisions"
comments: true
description: ""
category: heroku
tags: []
---

I was trying to add a database to my JSGames app -- it is a basic sinatra app with a few goodies already in place -- and I was having some difficulty deploying it to Heroku. Rails (for the most part) automagically creates and maintains its database, and as long as I remember to move any references to sqlite3 into the development and test environments, I have never had trouble provisioning one on Heroku either. Rolling a web application from scratch with sinatra, however, requires a little more savvy.
<!--more-->
My Gemfile had a familiar structure:

{% highlight ruby %}
source 'https://rubygems.org'
ruby '2.0.0'
...
gem "sinatra"
gem "activerecord"
gem "sinatra-activerecord"
gem "pg"{% endhighlight %}

So far, so familiar. I added config/environments.rb to outline the details of the database I would like provisioned (using postgres locally as well as in production):

{% highlight ruby %}
require 'active_record'
require 'uri'
...
db = URI.parse(ENV['DATABASE_URL'] || 'postgres://localhost/mydb')
ActiveRecord::Base.establish_connection(
  :adapter  => db.scheme == 'postgres' ? 'postgresql' : db.scheme,
  :host     => db.host,
  :port     => db.port,
  :username => db.user,
  :password => db.password,
  :database => db.path[1..-1],
  :encoding => 'utf8'
) {% endhighlight %}

With everything complete, I created a couple of models, migrated, tested it locally, and committed the changes. Heroku compiles the slug just fine, but when I open the application I get the too familiar "Application Error" page. The logs tell me: <pre>ERROR: Invalid DATABASE_URL</pre>

Huh? My url is the one that Heroku suggested! How can it be invalid? It was working fine locally, what is the problem? Here is a sample of my google searches from that frantic afternoon:
* ERROR: Invalid DATABASE_URL heroku
* deploying sinatra pg heroku
* environment variables
* heroku config variables
* usr-env-compile heroku
* heroku can't connect to server
* how to roll back a push to heroku
* how to recover from the embarrassment of an offline portfolio project
* how to start a llama farm

I finally found the answer (aka read the manual) with [Heroku Postgres](https://devcenter.heroku.com/articles/heroku-postgresql)

###First: Check to see if your database has been provisioned
{% highlight bash %}
$ heroku addons | grep POSTGRES
{% endhighlight %}

I ran this command in my sinatra project and found nothing. NOTHING!

When Heroku told me that it couldn't find my database, that is because it hadn't MADE my database. So I...

###Second: Create a new database
{% highlight bash %}
$ heroku addons:add heroku-postgresql:dev
Adding heroku-postgresql:dev on js-games... done, v42 (free)
Attached as HEROKU_POSTGRESQL_GOLD_URL
Database has been created and is available
 ! This database is empty. If upgrading, you can transfer
 ! data from another database with pgbackups:restore.
Use `heroku addons:docs heroku-postgresql:dev` to view documentation.{% endhighlight %}

YAY! My database has been created and found. Still, nothing wrong with...

###Third: Double check your database exists
{% highlight bash %}
$ heroku config
=== js-games Config Vars
HEROKU_POSTGRESQL_GOLD_URL: postgres://∏∏∏∏∏∏∏∏∏∏∏∏∏∏:∏∏∏∏∏∏∏∏∏∏∏@ec2-##-###-###-##.compute-1.amazonaws.com:####/∏∏∏∏#∏##∏∏∏#∏#{% endhighlight %}

Heroku confesses that my database is stored on AWS, and gives a fairly detailed address (redacted on the grounds of national security).

###Fourth: Promote the new database to primary
{% highlight bash %}
$ heroku pg:promote HEROKU_POSTGRESQL_GOLD_URL
Promoting HEROKU_POSTGRESQL_GOLD_URL to DATABASE_URL... done{% endhighlight %}

###Fifth: Check out the new database
{% highlight bash %}
$ heroku pg:info
=== HEROKU_POSTGRESQL_GOLD_URL (DATABASE_URL)
Plan:        Dev
Status:      available
Connections: 1
PG Version:  9.2.4
Created:     2013-07-27 22:46 UTC
Data Size:   6.3 MB
Tables:      0
Rows:        0/10000 (In compliance)
Fork/Follow: Unsupported {% endhighlight %}

### TADA
Do one final push to heroku and restart the server:
{% highlight bash %}
$ git commit -a -m "fixes the last two hours of disasterous troubleshooting"
$ git push heroku master
...
$ heroku restart
{% endhighlight %}


Awesome