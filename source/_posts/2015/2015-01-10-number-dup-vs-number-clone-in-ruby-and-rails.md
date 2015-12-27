---
layout: post
title: "#dup vs #clone in Ruby and rails"
date: 2015-01-10
comments: true
categories: [ruby, rails]
---

I was recently fixing a failing test and discovered that Ruby and Rails implement `#clone` and `#dup` in confusing and occasionally opposite ways.
<!--more-->

### In Rails `#clone` is a less complete copy of an object than `#dup`

Rails versions have flip-flopped on how to implement `#clone` and `#dup`, and there is ambiguity in how Rails defines "shallow". In Rails 4.0, [`#clone` is a shallow copy](https://github.com/rails/rails/blob/4-0-stable/activerecord/lib/active_record/core.rb#L217-L220) of an ActiveRecord object. "Shallow" in this context means that the `clone` shares attributes with the `original`:
> Identical to Ruby's clone method.  This is a "shallow" copy.  Be warned that your attributes are not copied. That means that modifying attributes of the clone will modify the original, since they will both point to the same attributes hash. If you need a copy of your attributes hash, please use the #dup method.

However, [`#dup` is also described as a shallow copy](https://github.com/rails/rails/blob/4-0-stable/activerecord/lib/active_record/core.rb#L234-L240). "Shallow" in this context means that while the dup does not share attributes with the original, it does share associations.
> Duped objects have no id assigned and are treated as new records. Note that this is a "shallow" copy as it copies the object's attributes only, not its associations. The extent of a "deep" copy is application specific and is therefore left to the application to implement according to its need.

#### `clone` vs `dup` in Rails:
```bash
pry> original = User.find(3)
  User Load (0.7ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT 1  [["id", 3]]
=> #<User id: 3, first_name: "katie", last_name: "leonard", email: nil, created_at: "2015-01-10 17:37:00", updated_at: "2015-01-10 17:37:00">

pry> clone_copy = original.clone
=> #<User id: 3, first_name: "katie", last_name: "leonard", email: nil, created_at: "2015-01-10 17:37:00", updated_at: "2015-01-10 17:37:00">

pry> dup_copy = original.dup
=> #<User id: nil, first_name: "katie", last_name: "leonard", email: nil, created_at: nil, updated_at: nil>
```

Note that the `clone_copy` is an exact copy of the original (same `user.id`) and the `dup_copy` is a new record (`user.id` = nil). Any changes made to the `clone_copy` will be changed in the `original`, but any changes to the `dup_copy` attributes will remain isolated.

### In Ruby `#clone` is a more complete copy of an object than `#dup`

With simple classes, `clone()` and `dup()` behave identically:

```bash
irb> class User
irb>   attr_accessor :first_name, :last_name, :email
irb>   def initialize(options={})
irb>     @first_name = options[:first_name]
irb>     @last_name  = options[:last_name]
irb>     @email      = options[:email]
irb>   end
irb> end
=> :initialize

irb> original = User.new(first_name: "katie", last_name: "leonard")
=> #<User:0x007fd7e98e0aa8 @first_name="katie", @last_name="leonard", @email=nil>

irb> cloned_copy = original.clone
=> #<User:0x007fd7e98c87c8 @first_name="katie", @last_name="leonard", @email=nil>

irb> dup_copy = original.dup
=> #<User:0x007fd7e98b24a0 @first_name="katie", @last_name="leonard", @email=nil>

irb> cloned_copy.first_name = "foo"
=> "foo"

irb> original.first_name
=> "katie"

irb> dup_copy.first_name
=> "katie"

irb> dup_copy.first_name = "bar"
=> "bar"

irb> original.first_name
=> "katie"
```
`clone()` and `dup()` function the same way!

`clone()` from the [Ruby docs](http://ruby-doc.org/core-2.1.5/Object.html#method-i-clone):
> Produces a shallow copy of obj — the instance variables of obj are copied, but not the objects they reference. Copies the frozen and tainted state of obj. See also the discussion under Object#dup.


`dup()` from the [Ruby docs](http://ruby-doc.org/core-2.1.5/Object.html#method-i-dup) looks suspiciously like the docs for `clone()`:

> Produces a shallow copy of obj — the instance variables of obj are copied, but not the objects they reference. dup copies the tainted state of obj. This method may have class-specific behavior. If so, that behavior will be documented under the #initialize_copy method of the class.

This deserves further clarification:

> In general, clone and dup may have different semantics in descendant classes. While clone is used to duplicate an object, including its internal state, dup typically uses the class of the descendant object to create the new instance. When using dup any modules that the object has been extended with will not be copied.

To paraphrase, `#dup` will act like `#clone`, but without the original's singleton class (ergo a "shallower" copy).

```
irb> class User
irb> attr_accessor :first_name, :last_name, :email
irb>   def initialize(options={})
irb>     @first_name = options[:first_name]
irb>     @last_name  = options[:last_name]
irb>     @email      = options[:email]
irb>   end
irb> end
=> :initialize

irb> module Crunchy
irb>   def bacon
irb>     "bacon"
irb>   end
irb> end
=> :bacon

irb> a = User.new(first_name: "katie", last_name: "leonard")
=> #<User:0x007fd7e8882490 @first_name="katie", @last_name="leonard", @email=nil>

irb> a.extend(Crunchy)
=> #<User:0x007fd7e8882490 @first_name="katie", @last_name="leonard", @email=nil>

irb> a.bacon
=> "bacon"

irb> b = a.clone
=> #<User:0x007fd7e8843060 @first_name="katie", @last_name="leonard", @email=nil>

irb> b.bacon
=> "bacon"

irb> c = a.dup
=> #<User:0x007fd7e98f05c0 @first_name="katie", @last_name="leonard", @email=nil>

irb> c.bacon
NoMethodError: undefined method `bacon' for #<User:0x007fd7e98f05c0>
        from (irb):101
        from /usr/local/var/rbenv/versions/2.1.5/bin/irb:11:in `<main>'
```

There are subtle differences between `#clone` and `#dup` in Ruby, and less subtle differences in Rails (depending on your version). Take care that the object you want is the object you get.
