---
layout: post
title:  "I admit it, I love TDD"
date:   2013-06-14
categories: TDD testing
---
This week @PDXcodeschool we jumped into TDD using minitest/autorun. I have
used RSpec in the past, and loved the way it let me manage the logic of my project.
I love that ruby has a built in framework -- so easy to use!

* Use specs to rough out project logic
{% highlight ruby %}
describe "Car" do
  it 'should have a color'
  it 'should have a make'
  it 'should have a unique serial number'
  it 'should get a new serial number when it gets stolen'
  it 'should accept passengers'
  it 'should not accept more than 3 passengers'
end
{% endhighlight %}
All of these tests will be skipped until they are followed by a do...end block.

* Take one test at a time and write the code to make it pass
{% highlight ruby %}
describe "Car" do
  it 'should have a color' do
    car = Car.new("black")
    car.color.must_equal "black"
  end
  ...
end
{% endhighlight %}
If your tests exercise the project well enough, you end up with really tight code.
More of a lasagna noodle than spaghetti!