---
layout: post
title: "scopes and single table inheritance"
date: 2015-01-22
comments: true
categories: [rails, sti]
---

Single table inheritance (STI) is a way to emulate object-oriented inheritance in a relational database[^1] by storing multiple object types in one table, distinguishable by a discriminator column such as `type`. Mixing levels of abstraction may make `join` operations easier, but it also makes other queries more complicated. In Rails 3.2, a query optimization was introduced that had some unintended consequences for STI.
<!--more-->

A scope represents a narrowing of a database query, and a named scope is syntactic sugar for defining a class method at runtime.

{% codeblock lang:ruby %}
class Shirt < ActiveRecord::Base
  scope :in_style, -> { where('purchase_date >= ?', Time.now - 2.months)}
end

class PoloShirt < Shirt
  scope :red, -> { where(color: 'red') }
  scope :blue, -> { where(color: 'blue') }
end

class SweatShirt < Shirt
  scope :logo, -> { where(logo: 'dragon' )}
end

PoloShirt.red.in_style
{% endcodeblock %}

The scope `:in_style` is converted into a class method behind the scenes at runtime, and is defined on the singleton class where the scope was named, not on the caller. While this detail has no consequences for objects outside of an inheritance scheme, it means that when PoloShirt invokes the `:in_style` scope, the class method is declared on Shirt, not PoloShirt.

Here is where the history lesson begins. Scopes have evolved in Rails, and while they remain syntactic sugar for definition class methods, the details, method signature, and sql translation have differed dramatically.

In [Rails 3.0](http://www.rubydoc.info/docs/rails/3.0.0/ActiveRecord/NamedScope/ClassMethods:scope), the scope method accepts a name, scope_options, and optional block. Scopes are directly translated into class methods behind the scenes, and the consequences of chaining scopes are the same as chaining queries, just nicer looking.

{% raw %}
```
PoloShirt.red.in_style.to_sql
=> SELECT * from shirts WHERE color = 'red' where type = 'PoloShirt' where purchase_date >= 1417102745 where type IN ('PoloShirt', 'Sweatshirt')
```
{% endraw %}

Notice the implicit `where` clause in STI. The `:red` scope, declared on PoloShirt has `where type = 'PoloShirt'`, and the `:in_style` scope, declared on Shirt, has `where type IN ('PoloShirt', 'Sweatshirt')`. The second `where` clause will have no impact on the query results, because they are already scoped to 'PoloShirt'. It is this behaviour that evolves over time.

The first jump in `scope` evolution is in [Rails 3.2](http://www.rubydoc.info/docs/rails/3.2.8/ActiveRecord/Scoping/Named/ClassMethods:scope), where the scope_options can include lambdas. Passing a lambda is a big advantage in that it allows the scope to be re-evaluated each time it is called. Unfortunately the implementation also remixes the query parameters of all chained scopes before evaluation:

> In nested scopings, all previous parameters are overwritten by the innermost rule, with the exception of `where`, `includes`, and `joins` operations in Relation, which are merged.

{% raw %}
```
PoloShirt.red.in_style.to_sql
=> SELECT * from shirts where color = 'red' where purchase_date >= 1417102745 where type IN ('PoloShirt', 'Sweatshirt')
```
{% endraw %}

The first `where type =` clause is merged with the last `where type IN` clause. The results of this query will no longer be scoped to PoloShirts, but will return all `red.in_style` Shirts of any type. Bad news for STI.

The good news is that this behaviour is [fixed](https://github.com/rails/rails/commit/cd26b6ae) in [Rails 4.0](http://www.rubydoc.info/docs/rails/4.0.0/ActiveRecord/Scoping/Named/ClassMethods#scope-instance_method). The latest evolution of scopes no longer allow you to pass a non-callable object (like a hash), and all scopes are merged using AND.

{% raw %}
```
PoloShirt.red.in_style.to_sql
=> SELECT * from shirts where color = 'red' where purchase_date >= 1417102745 where type = 'PoloShirt' AND type IN ('PoloShirt', 'Sweatshirt')
```
{% endraw %}

Our `red.in_style` query is once again scoped to the right level of the inheritance heirarchy. Named scopes are chainable and lazy-evaluated, making them a powerful query-building tool that can be difficult to troubleshoot -- especially when they are mixed with single-table inheritance.

[^1]: http://en.wikipedia.org/wiki/Single_Table_Inheritance