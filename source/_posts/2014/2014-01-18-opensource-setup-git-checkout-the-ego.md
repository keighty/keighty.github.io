---
layout: post
title: "open-source setup:"
date: 2014-01-18 11:18:29 -0800
comments: true
categories: opensource
---

##``git checkout`` the Ego.

In the interests of giving back to the software community, I have been looking for an active open-source project to contribute to. I am working to gain mastery of ruby on rails, so naturally I gravitate towards projects of that ilk. Unfortunately, I have gained just enough knowledge to be a little smug, so when I found a project I wanted to work on I scrolled immediately to the README and started scanning.<!--more--> The first block of text was headed by:

###Start here if this is your first ruby on rails project...

...with a link to a blog post. I kept scanning for information that would be relevant to ME. This was not my first ruby on rails project. I have my elaborate dev environment set up just the way I like it, thank you very much, and I can reproduce your project with a flick of my config file, I am sure (chuckles condescendingly). This is a snippet of the proceeding workflow:

```bash Workflow of the egotistical programmer.
$ git clone repo
$ cd repo/
$ bundle install
Errors: need to update your rubygems
$ gem update --system
$ bundle exec rake db:setup
Errors: need postgres installed (no inkling in the README)
$ brew install postgres
... follow half of the required instructions
Errors
Frustration because you don't know how to follow instructions
$ brew info postgres
...
"ooooohhhhhhhh... I missed a step"
$ follow instructions this time
$ bundle exec rake db:setup
Errors: need redis installed
$ brew install redis
$ follow instructions because I have learned my lesson
$ bundle exec rake db:setup
Errors: redis config file missing information
Frustration because redis config file was removed 100 commits ago
...
Determine that first pull request
will be an amendment to setup documentation
Rebuild config file and add host name
$ bundle exec rake db:setup
Errors: required seeds file was removed 100 commits ago
$ bundle exec rake db:migrate
$ SUCCESS!!!
$ bundle exec rake spec
Errors: run db:test:prepare
$ bundle exec rake db:test:prepare
Errors
Frustration
$ RAILS_ENV=test bundle exec rake db:migrate
$ SUCCESS!!
$ bundle exec rake spec
Errors... sigh.. more errors
$ limp away for lunch, live to contribute another day
```

Getting a new project setup and functional is not an easy chore, and I was quite chastened when I swallowed my pride and checked out the blog post for first timers, which boiled down to:

1. Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Install [Vagrant](http://www.vagrantup.com/)
3. ```git clone repo```
4. ```cd repo```
5. ```vagrant up```
6. ```vagrant ssh```
7. ```cd /vagrant```
8. ```bundle install```
9. ```bundle exec rake db:migrate```
10. ```bundle exec rails s```

I could have been contributing hours ago, when I still had momentum! Perhaps my first pull request should still be to modify the README. I would rephrase the heading to read "For all developers: get started right here", and then remove every other instruction that followed.

Perhaps I should go get started...

Awesome.