---
layout: post
title: "cleaning the campground"
date: 2015-01-10
comments: true
categories: [read]
---

If you work in a code base with many other contributors, you may have learned the scouting philosophy to "leave the campground cleaner than you found it." Sometimes the code you are working in can seem so entangled and complex that it is hard to know where to begin, but [Robert C. Martin's foundational book, *Clean Code](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_1?ie=UTF8&qid=1420928385&sr=8-1&keywords=clean+code)*, tells us that very small changes can improve the readability and maintainability of a piece of code.

>"It is not enough to write the code well. The code has to be *kept clean* over time."
<!--more-->

[Code rot](http://en.wikipedia.org/wiki/Software_rot) happens to every well-intentioned but long-lived code base, and it can happen either by neglect or by constant development. {% pullquote %}Neglected software will suffer a gradual deterioration in performance and responsiveness over time. Security vulnerabilities and incompatibilities aside, as new applications are built in newer and faster frameworks, your application will perform poorly in comparison. If your software is actively developed it will be modified, and as it is modified it's complexity will increase. {"Add in more developers, each with different coding styles, and before long you will find your code to be impenetrably complex and impossible to modify."}
{% endpullquote %}

There is hope for your legacy application, as long as you establish a culture of constant small improvements:

1. Change one variable name for the better
2. Break one large function into two or more smaller ones.
3. Eliminate one small bit of duplication
4. Clean up one composite if statement.

That last one is a bit like low-hanging fruit. Take this complex if statement in [Rack::Deflater#call](https://github.com/rack/rack/blob/rack-1.5/lib/rack/deflater.rb#L30-L33)

```
def call
  ...

  # Skip compressing empty entity body responses and responses with
  # no-transform set.
  if Utils::STATUS_WITH_NO_ENTITY_BODY.include?(status) ||
      headers['Cache-Control'].to_s =~ /\bno-transform\b/ ||
     (headers['Content-Encoding'] && headers['Content-Encoding'] !~ /\bidentity\b/)
    return [status, headers, body]
  end
  ...
```

The comment attempts to explain what the code cannot: skip response compression under certain conditions. The intention could easily be described by extracting both the comment and the if statement to a method:

```
def call
  ...
  if skip_compression?
    return [status, headers, body]
  end
  ...
end

private

# Skip compressing empty entity body responses and responses with
# no-transform set.
def skip_compression?
  Utils::STATUS_WITH_NO_ENTITY_BODY.include?(status) ||
    headers['Cache-Control'].to_s =~ /\bno-transform\b/ ||
   (headers['Content-Encoding'] && headers['Content-Encoding'] !~ /\bidentity\b/)
end
```

While the `if` statement itself is not much better, at least `#call` is a little more readable. Fight against code rot while building new features in a legacy code base by making small improvements where you can.

<!-- resources
http://en.wikipedia.org/wiki/Software_entropy
-->