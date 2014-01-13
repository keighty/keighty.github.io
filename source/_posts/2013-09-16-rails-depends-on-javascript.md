---
layout: post
title: "Rails, You Can Depend on Javascript"
comments: true
description: ""
category: rails
tags: []
---

I am between computers at the moment, and I really really miss my OSX development environment. Dependency installation is so smooth and painless on mac, that I had quite forgotten the complications of setting up a complete environment in linux. For instance

###Rails requires a javascript runtime environment
I am running an Ubuntu 9.4 remix on my little Atom Netbook. Getting rails up and running anew is a lesson in returning to fundamentals.
{% highlight bash %}
$ gem install rails
$ rails new testapp -T
$ rails generate rspec:install
...
Could not find a JavaScript runtime. See https://github.com/sstephenson/execjs for a list of available runtimes{% endhighlight %}
<!--more-->
###Why does rails use a javascript runtime?
A web application does not exist in a ruby/rails vacuum -- rendering an html page requires css and javascript as well. By default, the 'rails new' command generates a Gemfile containing a few suggested goodies, including uglifier, which compresses javascript assets. Dealing with multiple layers of javascripts can hurt application performance, which is why rails has adopted a compression strategy. Uglifier minifies your javascript by removing all the whitespace.

From the RailsGuides:
>You will need an ExecJS supported runtime in order to use uglifier. If you are using Mac OS X or Windows you have a JavaScript runtime installed in your operating system. Check the ExecJS documentation for information on all of the supported JavaScript runtimes.

###Setup Node.js on Linux
In Ubuntu 10.4 and above, a JavaScript runtime is included, but installing Node on an older Ubuntu distro is not as simple as
{% highlight bash %}
$ sudo apt-get nodejs{% endhighlight %}

I found the key sequence of commands on the Node.js website. First, update your packages and install node.js dependencies: python, g++, make, etc
{% highlight bash %}
$ sudo apt-get update
$ sudo apt-get install python-software-properties python g++ make {% endhighlight %}

Next, add the location of the nodejs repository, update, and install.
{% highlight bash %}
$ sudo add-apt-repository ppa:chris-lea/node.js
$ sudo apt-get update
$ sudo apt-get install nodejs {% endhighlight %}

Wow -- with all of that done, I can finally:
{% highlight bash %}
$ rails generate rspec:install
      create  .rspec
      create  spec
      create  spec/spec_helper.rb {% endhighlight %}

Awesome.

