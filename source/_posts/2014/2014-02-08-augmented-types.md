---
layout: post
title: "augmented types"
date: 2014-02-08
comments: true
categories: [javascript]
---

Javascript and Ruby have many features in common, including the ability to add functionality to existing objects.
<!--more-->

##Ruby monkeypatching
Ruby allows you to reopen classes to add new functionality. For example, I can reopen the String class to add a new method, "foo()":

```bash
irb > class String
irb >   def foo
irb >     "I see you!"
irb >   end
irb > end
# => nil
irb > "".class
# => String
irb > "".foo
# => "I see you!"
```

##Javascript augmenting
Javascript allows you to augment existing objects with additional functionality.

```javascript
> String.prototype.foo = function foo() {
    return "I see you!";
  }
function foo() {
    return "I see you!";
  }
> typeof("");
"string"
> "".foo()
"I see you!"
```