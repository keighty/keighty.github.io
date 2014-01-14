---
layout: post
title: "Canadian Flair"
date: 2013-08-10 21:29:03 -0800
comments: true
description: ""
category: eh
tags: []
---

For my last month at code school, I have decided to learn what it takes to develop a new programming language. Having been teased for my Canadian accent, I thought it appropriate to incorporate some idiomatic Canadianisms, and call my language Eh? Deciding what actual features I want to implement in my programming language is an interesting task, and I think I have narrowed it down to a couple of standard rules and a couple wacky ones:

### Rules for Eh?
* blocks of code are delimited by ':' and 'eh?'
* classes are declared using "A" keyword
* methods are defined using "CAN" keyword
* lowercase identifiers are local variables or method names
* capitalized identifiers are global variables
* no parens for args
* last value evaluated in return value
* everything is an object
<!--more-->
#Steps for building an interpreted language
1. This code has to be provided as input to a lexer.
1. The lexer will convert that input into tokens.
1. The parser will organize those tokens into a tree of nodes.
1. The runtime will evaluate the nodes using ruby.

###Prototype
Here is what I would like the final result to look like:

{% highlight ruby %}
a Canadian
  with toque
  with scarf
  with broom

  can curl
    if skip:
      say "Hurry!"
    eh?
    if lead:
      pass
    eh?
    say "How social the game..."
  eh?

  can say_aboot:
    say "What's it all aboot?"
  eh?
eh?
{% endhighlight %}

With this vague action plan and ["Create Your Own Programming Language"](http://createyourproglang.com/?hop=rubyinside) by Marc-Andre Cournoyer in hand, off we go!

Awesome