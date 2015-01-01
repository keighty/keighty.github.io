---
layout: post
title: "augmented objects"
date: 2014-02-09
comments: true
categories: [javascript]
---

Because it bears repeating: javascript and ruby have many features in common, including the ability to add functionality to existing objects.
<!--more-->

##Ruby singleton
You can add methods to an instance of a class without affecting any other instances of that class.
```bash
> x, y = "stringA", "stringB"
> def x.baz
>   puts "I can haz?"
> end
# => nil
> x.baz
I can haz?
# => nil
> y.baz
NoMethodError: undefined method `baz' for "stringB":String
  from (irb):62
  from /home/katherine/.rvm/rubies/ruby-2.0.0-rc1/bin/irb:12:in `<main>'
```

## Javascript augmenting
You can add methods to an of an Array object without affecting any other array objects.

```
> var data1 = [1, 2, 3, 4, 5, 6];
undefined
> var data2 = [2, 4, 6, 8, 10, 12];
undefined
> data1.total = function() { return 21; };
function () { return 21; }
> data1.total();
21
> data2.total()
TypeError: Object [object Array] has no method 'total'
```