---
layout: post
title: "global abatement"
date: 2014-02-07
comments: true
categories: [I didn't know that, javascript]
---

###or, What the heck is global abatement?

**tldr**: Global variables are [code smells](http://en.wikipedia.org/wiki/Code_smell). Declaring variables inside an application-level function serves to namespace the variables and minimizes the use of global variables. <!--more-->

Javascript has function-level scoping, which means that variables declared anywhere within a function are scoped to that function. Writing a javascript without an enclosing function leaves your declared variables vulnerable to name collisions, or reassignment by other javascripts.

A **global abatement** is a strategy used to reduce the vulnerability of your javascript variables by removing them from the global scope. Application namespacing is one such strategy:

{% codeblock lang:javascript BAD GLOBAL%}
person = {
  first_name: 'Hello',
  last_name: 'Kitty'
};
{% endcodeblock %}

{% codeblock lang:javascript GOOD LOCAL%}
var MYAPP = {};
MYAPP.person = {
  first_name: 'Hello',
  last_name: 'Kitty'
};
{% endcodeblock %}

Declaring an application-level function ensures that the correct ```person``` is called when needed. It improves readability by essentially flagging the scope of the variable and reducing ambiguity.

[JavaScript: The Good Parts](http://www.amazon.com/gp/product/0596517742/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0596517742&linkCode=as2&tag=bridgeforpoke-20)
