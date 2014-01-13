---
layout: post
title: "Red, Green, Refactor! A Workflow for Rails"
comments: true
description: ""
category: rails
tags: []
---

###1. Write a failing test
{% highlight ruby %}
describe "Static About page" do
  it "should have the content 'About Us'" do
    visit '/static_pages/about'
    page.should have_content('About Us')
  end
end{% endhighlight %}

Use the failures to drive development:

###2. No Route
{% highlight bash %}
Failures:
  1) Static About page should have the content 'About Us'
     Failure/Error: visit '/static_pages/about'
     ActionController::RoutingError:
       No route matches [GET] "/static_pages/about"{% endhighlight %}

Add one to /config/routes.rb:
{% highlight ruby %}
DemoApp2::Application.routes.draw do
  get "static_pages/about"
  ...
end{% endhighlight %}

###3.No Controller
{% highlight bash %}

Failures:

  1) Static About page should have the content 'About Us'
     Failure/Error: visit '/static_pages/about'
     AbstractController::ActionNotFound:
       The action 'about' could not be found for StaticPagesController
{% endhighlight %}

Add one to /controllers/static_pages_controller.rb

{% highlight ruby %}
class StaticPagesController < ApplicationController
  def about
  end
end{% endhighlight %}

###4. No Page
{% highlight ruby %}
Failures:
  1) Static About page should have the content 'About Us'
     Failure/Error: visit '/static_pages/about'
     ActionView::MissingTemplate:
       Missing template static_pages/about, application/about with {:locale=>[:en], :formats=>[:html], :handlers=>[:erb, :builder, :raw, :ruby, :jbuilder, :coffee]}.
{% endhighlight %}

Add a view to /static_pages/about.html.erb

{% highlight html %}

<!DOCTYPE html>
  <body>
    <h1>About Us</h1>
  </body>
</html>{% endhighlight %}
