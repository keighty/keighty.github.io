---
layout: post
title: "hunting monkey patches"
date: 2015-08-08
comments: true
sharing: true
categories: [rails]
---

Ruby monkeypatching is a dangerous, but necessary tool. It is convenient to add new behaviours to  existing classes, or to replace existing methods with more customized or secure code. Once the patch is in place, however, it is easy to forget that it is there.

Recently, I was trying to discern the origin of such a patch, and discovered a new (to me) function in pry, my favourite Ruby debugger: `show-source`.
<!--more-->

1. Put a `binding pry` below the monkeypatch so that it can be caught at boot time
2. Use `cd` to change directories into the reopened class
3. Use `show-source` to find the exact location where the method was originally defined
4. Use `show-source -a` to show the source and location of EVERY MONKEYPATCH

In action:

#### 1. Debug your code at boot time
{% img /images/150808-hunting-monkey-patches/step1-pry.png %}

#### 2. Reopen the class
{% img /images/150808-hunting-monkey-patches/step2-cd.png %}

#### 3. Use 'show-source'
{% img /images/150808-hunting-monkey-patches/step3-show-source.png %}

#### 4. Use 'show-source -a'
{% img /images/150808-hunting-monkey-patches/step4-show-source-a1.png %}
{% img /images/150808-hunting-monkey-patches/step4-show-source-a2.png %}
