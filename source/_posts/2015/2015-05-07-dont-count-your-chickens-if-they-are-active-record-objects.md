---
layout: post
title: "Don't count your chickens if they are ActiveRecord objects"
date: 2015-05-07
comments: true
sharing: true
categories: [rails]
---

You may believe that `#length`, `#size`, and `#count` are fairly equivalent:

```
irb(main):002:0> yourRubyChickens =  %w{ Chantecler RedShaver RhodeIslandRed }
=> ["Chantecler", "RedShaver", "RhodeIslandRed"]
irb(main):003:0> yourRubyChickens.length
=> 3
irb(main):004:0> yourRubyChickens.size
=> 3
irb(main):005:0> yourRubyChickens.count
=> 3
```

But, beware of `#count` in Rails!

```
[1] pry(main)> yourRailsChickens = Chicken.all
  Chicken Load (2.1ms)  SELECT "chickens".* FROM "chickens"
=> [#<ChanteclerChicken:0x007f98eafcd6d8
  id: 1,
  type: "ChanteclerChicken",
  color: "white",
  created_at: Thu, 07 May 2015 14:36:36 UTC +00:00,
  updated_at: Thu, 07 May 2015 14:36:36 UTC +00:00>,
 #<RedShaverChicken:0x007f98e63a9428
  id: 2,
  type: "RedShaverChicken",
  color: "white",
  created_at: Thu, 07 May 2015 14:37:01 UTC +00:00,
  updated_at: Thu, 07 May 2015 14:37:01 UTC +00:00>,
 #<RedShaverChicken:0x007f98e63a9248
  id: 3,
  type: "RedShaverChicken",
  color: "black",
  created_at: Thu, 07 May 2015 14:37:25 UTC +00:00,
  updated_at: Thu, 07 May 2015 14:37:25 UTC +00:00>,
 #<RedShaverChicken:0x007f98e63a9068
  id: 4,
  type: "RedShaverChicken",
  color: "red",
  created_at: Thu, 07 May 2015 14:37:35 UTC +00:00,
  updated_at: Thu, 07 May 2015 14:37:35 UTC +00:00>,
 #<RedShaverChicken:0x007f98e63a8e88
  id: 5,
  type: "RedShaverChicken",
  color: "white",
  created_at: Thu, 07 May 2015 14:37:53 UTC +00:00,
  updated_at: Thu, 07 May 2015 14:37:53 UTC +00:00>]

[2] pry(main)> yourRailsChickens.length
=> 5
[3] pry(main)> yourRailsChickens.size
=> 5
[4] pry(main)> yourRailsChickens.count
   (0.2ms)  SELECT COUNT(*) FROM "chickens"
=> 5
```

`#size` and `#length` are largely equivalent, but `#count` issues an additional query to the database that can seriously damage performance for the unsuspecting developer.

For more info, checkout the docs for [count](http://www.rubydoc.info/docs/rails/4.0.0/ActiveRecord/Calculations:count) and [length](http://www.rubydoc.info/docs/rails/4.0.0/ActiveRecord/Associations/CollectionAssociation:length).
