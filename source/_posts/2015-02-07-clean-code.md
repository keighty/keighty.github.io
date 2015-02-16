---
layout: post
title: "clean code"
date: 2015-02-07
comments: true
categories: [reads]
---


The book club I belong to has a two-fold mission: the first is to inspire through peer pressure to read (and finish) technical books, and the second is to improve our ability to write quality, maintainable code. We started with [*Practical Object Oriented Design in Ruby*](http://www.amazon.com/Practical-Object-Oriented-Design-Ruby-Addison-Wesley/dp/0321721330/ref=sr_1_1?ie=UTF8&qid=1423423708&sr=8-1&keywords=practical+object-oriented+design+in+ruby) (Sandi Metz), and are now on to [*Refactoring*](http://www.amazon.com/s/ref=nb_sb_ss_i_0_11?url=search-alias%3Daps&field-keywords=refactoring&sprefix=refactoring%2Caps%2C217) (Martin Fowler and Kent Beck). Along the way, I found a copy of [*Clean Code*](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_1?ie=UTF8&qid=1423423781&sr=8-1&keywords=clean+code) by Robert C. Martin in our work library, and dove right in. These three books tout many of the the same principles but focus on different aspects of writing quality code: POODR teaches how to identify abstractions, and to design decoupled objects; Refactoring is a deep dive into the techniques and heuristics for eliminating common code smells; and Clean Code is the intersection between them. It is a style guide, a call to action, and a cautionary tale from the voices of experience. I had several aha moments while reading Clean Code, and here are a few<!--more-->:

### Error handling
When I started to anticipate `undefined method 'length' for nil:NilClass` errors, I knew I was growing as a developer.
Having traced the source of `undefined is not a function` enough times, I came to realize that I was not sufficiently controlling the messages passing between objects.

{% codeblock lang:ruby Could return nil %}
def find_a_user
  @user = User.where(last_name: "Bacon").first
end
=> :find_a_user

find_a_user
@user.last_name

NoMethodError: undefined method `last_name' for nil:NilClass
from (pry):10:in `__pry__'
{% endcodeblock %}

This first pass seemed totally reasonable to me -- find a particular user, and return it. Of course, what if the user doesn't exist? What if the query timed out? The code on line 7 is reasonable (wherever it may live), but if `@user` is set to nil my app will blow up. {% pullquote %}The worst part is, it will blow up at quite a distance from where the actual problem is. A naive solution would be to check if `@user` is nil before calling `#last_name` on it (`@user.last_name if @user`), but this leads to unreadable, untrustworthy code. {"The problem isn't that the code is missing a null check, but that it has too many."} I have a poor contract with `User` if I have to check its validity every time I ask it a question. Martin suggests a more sustainable solution -- *never return `nil`*. If you never return `nil`, you don't have to catch `nil`.
{% endpullquote %}

> If you are calling a [nil]-returning method from a third-party API, consider wrapping that method with a method that either throws an exception or returns a special case object.

{% codeblock lang:ruby Never returns nil %}
def find_a_user
  if @user = User.where(last_name: "Bacon").first
    @user
  else
    raise StandardError.new "Could not find Mr. Bacon."
  end
end

find_a_user
=> StandardError: Could not find Mr. Bacon.
{% endcodeblock %}

Now the error occurs where it belongs -- with the retrieval of the user. Throwing an error when something goes wrong is the least you can do for the consumers of your method. You could also solidify your contract by guaranteeing that you return something they expect.

{% codeblock lang:ruby %}
def interesting_things
  @interesting_things || []
end

interesting_things.length
=> 0
{% endcodeblock %}

I have guaranteed that `#interesting_things` will always return either the collection I expect or a reasonable facscimile. It is a simple enough solution, but I definitely felt that message sink into place. Methods are objects and objects have API's: messages in and messages out. Make those messages deliberate, because if "returning null from methods is bad, passing null into methods is worse."

### Protect Your Boundaries
Controlling the messages going in to and coming out of your objects is one place you need to protect your boundaries, but what about objects from 3rd party libraries or frameworks? There are consequences of relying too heavily on a library API, and in Chapter 9: Boundaries, James Grenning shares a few of them. One system he was working with collected information by passing around a Map collection, which is a standard library data structure in Java. In the next version of Java, new capabilities were added, and in order to use the new feature they had to find everywhere the object was used and make the change.

> We've seen systems that are inhibited from using [the new feature] because of the sheer magnitude of changes needed to make up for the liberal use of Maps.

I recently had a similar experience when upgrading an application from Rails 3.0 to Rails 3.2, where there were drastic changes to how models record errors. Errors inherited from OrderedHash in Rails 3.0 and from ActiveModel in Rails 3.2 -- very different object types. Every test that looked for errors called `errors.on` -- now a deprecated method. Every place that looped over the collection with `errors.each` had to be changed to `errors.messages.each`, or the logic modified altogether. It is good practice to be distrustful of APIs that are likely to change, and while I don't believe that the Errors object ever seemed unstable, it has certainly made me extra cautious of calling 3rd party framework methods directly.

### Clean Code is not a "nice-to-have"
Technical debt is not a decision to be okay with untested, brittle code that is impossible to build on -- it is a decision to pursue a design that may not be scalable, but it can still be clean. It is tempting to declare "Mission Accomplished" when you have reached the point of working code, but the job is really only half done:

> It is not enough for code to work. Code that works is often badly broken. Programers who satisfy themselves with merely working code are behaving unprofessionally. They may fear that they don't have time to improve the structure and design of their code, but I disagree. Nothing has a more profound and long term degrading effect upon a development project than bad code. Bad schedules can be redone, bad requirements can be redefined. Bad team dynamics can be repaired. But bad code rots and ferments, becoming an inexorable weight that drags the team down.

A story cannot be declared finished until the code is ammenable to change. In order to change code, it must be comprehensible and thoroughly tested. Compromising code quality to meet the demands of a schedule will only slow things down in the long run.

*Clean Code* is a must read for all professional software developers. Leaving clean code is not just a kindness to the next developer (which is likely to be you), but is essential for growing a healthy codebase. Of course, "to write clean code, you must first write dirty code and then clean it," and I look forward to learning new techniques as we tackle *Refactoring*.
