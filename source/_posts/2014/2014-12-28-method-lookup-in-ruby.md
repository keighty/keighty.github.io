---
layout: post
title: "method lookup in ruby"
date: 2014-12-28
comments: true
categories: [ruby]
---

I have once more been working my way through Sandi Metz' [Practical Object-oriented Design in Ruby](http://www.amazon.com/Practical-Object-Oriented-Design-Ruby-Addison-Wesley/dp/0321721330) (POODR) for a few weeks, and developed a mental block about method lookup.

In Chapter 6 (Acquiring behaviour through inheritance), she describes how to extract a superclass from a group of related classes that share some behaviour by pulling methods up the inheritance chain instead of driving specializations down. This approach ensures a clean abstraction, leaving no specialized behaviour in the superclass. Where I got stuck was in the call chain that enforces the common interface:

```ruby
class Foo
  def initialize
    baz
  end

  def baz
    raise NotImplementedError.new "You can't call #baz here!"
  end
end

class Bar < Foo
  def initialize
    super
  end

  def baz
    puts "Hello, baz"
  end
end

> x = Bar.new
"Hello, baz"
=> #<Bar:0x007fc4f903ab90>
```

I could not understand why, the `NotImplementedError` wasn't raised when a new `Bar` was created. This is how I imagined the call stack should work:

1. `Bar.new` calls `Bar#initialize`
1. `Bar` calls `super` (`Foo#initialize`)
1. `Foo` calls `baz` (`Foo#baz`)
1. raises `NotImplementedError`

This is how the call stack actually works

1. `x = Bar.new` calls `Bar#initialize`
1. `x` receives a call to `super` (`Foo#initalize`)
1. `x` receives a call to `baz` (`Bar#baz`)
1. says "Hello, baz"

The call never comes from the context of `Foo` -- the receiver is always `x`, the instance of `Bar`. Classical inheritance dictates that an object can only look to itself or further up the inheritance chain for valid method definitions, not down. `x` is reaching up to call `super` but checks itself for an answer to the question `#baz`. `Foo#baz` would only ever be called if we made a new `Foo` directly:

```
> y = Foo.new
NotImplementedError: You can't call #baz here!
        from (irb):7:in `baz'
        from (irb):3:in `initialize'
        from (irb):27:in `new'
        from (irb):27
        from /usr/local/var/rbenv/versions/2.1.5/bin/irb:11:in `<main>'
```

### How ruby resolves a message (classical inheritance)
The search for a method begins in the class of the receiving object -- in the example above, Bar is always the receiving object. If the class does not implement the message, the search proceeds up the superclass chain. If none of the superclasses contain the method definition, ruby makes a second attempt to resolve the message by sending `method_missing(:method_name)` to the original object. The search restarts from the bottom, but this time for a `method_missing` handler rather than `:method_name`.

Occasionally refreshing the ruby basics is super-rewarding.