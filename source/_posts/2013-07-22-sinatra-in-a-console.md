---
layout: post
title: "Sinatra in a console"
description: ""
category: sinatra
tags: []
---

Sinatra does not have a default console like rails does. If you are writing in a rails environment and need to test out a helper method, or want to check on the state of a record in the database, dropping down into the rails console is as easy as typing:
{% highlight bash %}
$ rails c{% endhighlight %}

How can we achieve the same ability in Sinatra?
###Tux
Tux is a ruby gem that generates a sinatra console. It reads the config.ru, allows you to interact with App methods, and provides empty request and response objects to for investigating communication between views and the Sinatra controller.

Add Tux to your Gemfile:
{% highlight ruby %}
group :development, :test do
  ...
  gem 'tux'
end{% endhighlight %}

Don't forget to bundle!

At the command line, start up tux and use rack.actions to view all the methods available:
{% highlight bash %}
$ tux
Loading development environment (Rack 1.2)
>> rack.actions
=> [:request, :get, :post, :put, :patch, :delete, :options, :head, :follow_redirect!, :header, :set_cookie, :clear_cookies, :authorize, :basic_authorize, :digest_authorize, :last_response, :last_request]
>>{% endhighlight %}

You can access any method from your application as well.
Of course, if you are working in Sinatra, you probably want to go as lightweight as possible, which is why you can also use:

###Basic irb
It is as simple as using irb and requiring your main app file. You have access to all the dependencies and files that your application does by simply requiring that one file. You can check state, call methods, create objects, whatever you need:
{% highlight bash %}
$ irb
> require './app.rb'
 => true
> User.all
D, [2013-07-22T15:47:49.230146 #54081] DEBUG -- :   User Load (0.2ms)  SELECT "users".* FROM "users"
 => #<ActiveRecord::Relation [#<User id: 1, name: "Gordon Shumway", email: "gordon@example.com", created_at: "2013-07-22 22:45:48", updated_at: "2013-07-22 22:45:48">, #<User id: 2, name: "Fanny Eubanks", email: "fanny@example.com", created_at: "2013-07-22 22:45:48", updated_at: "2013-07-22 22:45:48">]>
>{% endhighlight %}

This can be called even more elegantly at the command line:
{% highlight bash %}
$ irb -r ./app.rb{% endhighlight %}

or through a Rake task:
{% highlight bash %}
desc "run irb console"
task :console, :environment do |t, args|
  ENV['RACK_ENV'] = args[:environment] || 'development'
  exec "irb -r irb/completion -r ./app.rb"
end{% endhighlight %}

Awesome