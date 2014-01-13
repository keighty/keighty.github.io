---
layout: post
title: "Factoring In Nests"
comments: true
description: ""
category: rails
tags: []
---

I am building an application that uses several nested attributes: Users have many Goals (Goals belong_to Users), and Goals have many Hours (Hours belong_to Goals).

Using [FactoryGirl](https://github.com/thoughtbot/factory_girl) to create a User is straightforward:

{% highlight ruby %}
FactoryGirl.define do
  factory :user do
    sequence :name  { |n| "person#{n}" }
    sequence :email { |n| "person#{n}@example.com" }
  end
end

describe User do
  before  { @user = FactoryGirl.create(:user) }
  subject { @user }
  it { should respond_to(:name) }
  ...
end {% endhighlight %}

How do I create Goals that are linked to the User?
### How to create Factories for nested attributes

#####1. Make a new Factory for producing Goals:
{% highlight ruby %}
FactoryGirl.define do
  factory :goal do
    sequence :description { |n| "my goal #{n}" }
    sequence :motivation  { |n| "my motivation #{n}" }
  end
end {% endhighlight %}

#####2. Call the Goal Factory from the User Factory and store the Goals in an Array
{% highlight ruby %}
FactoryGirl.define do
  factory :user do
    sequence :name { |n| "person#{n}" }
    sequence :email { |n| "person#{n}@example.com" }

    goals { Array.new(1) { FactoryGirl.build(:goal) } }

  end
end {% endhighlight %}

Your objects will look like this:
{% highlight bash %}
> p @user
#<User id: 1, name: "person1", email: "person1@example.com", ... >
> p @user.goals.first
#<ActiveRecord::Associations::CollectionProxy [#<Goal id: 1, user_id: 1, description: "my goal 1", motivation: "my motivation 1", ...]> {% endhighlight %}

Create Hours for Goals in the same manner: create a Factory for making Hours, call the Hours Factory from the Goals Factory.

{% highlight ruby %}
FactoryGirl.define do
  factory :hour do
    duration { 100 * rand(36) }
    ...
  end
end

FactoryGirl.define do
  factory :goal do
    sequence :description { |n| "my goal #{n}" }
    sequence :motivation  { |n| "my motivation #{n}" }

    hours { Array.new(3) { FactoryGirl.build(:hour) } }
  end
end {% endhighlight %}

Checking your objects:
{% highlight bash %}
> p @user
#<User id: 1, name: "person1", email: "person1@example.com", ... >
> p @user.goals.first
#<ActiveRecord::Associations::CollectionProxy [#<Goal id: 1, user_id: 1, description: "my goal 1", motivation: "my motivation 1", ...]>
> p @user.goals.first.hours.first
#<ActiveRecord::Associations::CollectionProxy [#<Hour id: 1, goal_id: 1, duration: 3200 , ...]> {% endhighlight %}

Awesome.
