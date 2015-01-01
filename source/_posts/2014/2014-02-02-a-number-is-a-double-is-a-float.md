---
layout: post
title: "a number is a double is a float"
date: 2014-02-02 19:56:17 -0800
comments: true
categories: [I didn't know that, javascript]
---

JavaScript has a single number type -- a 64-bit floating point number. Since there is no separate integer type, 1 and 1.0 are the same value.
<!--more-->

One of the consequences is that the ```/``` operator may return a floating point number even if both operands are integers. Goodbye casting!

[JavaScript: The Good Parts](http://www.amazon.com/gp/product/0596517742/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0596517742&linkCode=as2&tag=bridgeforpoke-20)

Awesome.
