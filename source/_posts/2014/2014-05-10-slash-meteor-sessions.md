---
layout: post
title: "meteor sessions"
date: 2014-05-10
comments: true
categories: [meteor, javascript]
published: false
---

## Session
* stores information that relates to a single user's instance of your application
* "global reactive data store":
** there is only one session per application instance
** the information in the session is as reactive as the database information store

## Set a session variable
{% codeblock Setting a session variable lang:js %}
> Session.set('myVariable', 'myVariableValue');
{% endcodeblock %}

## Retrieving a session variable
{% codeblock Getting a session variable lang:js %}
> Session.get('myVariable');
{% endcodeblock %}

## Autorun
Yes, the session is a reactive data store, meaning it can be altered or added to at will. Why doesn't it automagically update my UI? Because the __context__ in which the Session is altered or added to may not be a reactive context. When the application is opened for the first time, the Session is read from and added to as the application code is loaded, but unless the Session changes are included in an Autorun block, meaning it is re-run every time there are changes made to it, the Session and all its fancy state-ness is only loaded the once.
<!--more-->


## Lessons about sessions
1. Always store user state in the Session or the URL so that users are minimally disrupted when a hot code reload happens.
2. Store any state that you want to be shareable between users within the URL itself.