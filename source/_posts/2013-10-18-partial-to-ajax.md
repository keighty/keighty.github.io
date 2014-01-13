---
layout: post
title: "Partial to Ajax"
comments: true
description: ""
category: rails
tags: []
---

I have been building a new project and getting creative with views. I am working with bootstrap and jquery to develop tabbed displays, and in the process have been gleaning a deeper understanding of rails routes, controllers, and assets. Using Ajax to load partials into rails views is a three part process:
<!--more-->
####1. In the View, Add a Link
/path/to/view.html.erb

{% highlight ruby %}
<%= link_to "Show my partial", path_to_controller, remote: true %> {% endhighlight %}

The key difference in the syntax for this link is "remote: true", which signals to the controller action to respond using ajax, not html.

####2. Create a Partial
/path/to/view/_my_partial.html.erb
{% highlight ruby %}
<div>
  <%= form_for @model do |f| %>
    <%= f.label :my_text_field, "Text Field Label" %>
    <%= f.text_area :my_text_field %>
    <%= f.submit "New", class: "btn btn-primary" %>
  <% end %>
</div>
{% endhighlight %}

####3. In the Controller Action, Add a js Response
/path/to/controller.rb

{% highlight ruby %}
def my_action
  respond_to do |format|
    format.js
  end
end{% endhighlight %}

Normally, Rails will render the view that corresponds to the name of the action in response to an html request. The format.js line asks rails to look for a javascript file instead of an html file when it tries to render a view.

####3. Create a my_action.js.erb File to Shape the Ajax Response
/path/to/view/my_action.js.erb

{% highlight javascript %}
$("#your-placeholder-id").prepend('<%= escape_javascript(render 'path/to/view/my_partial') %>
{% endhighlight %}

Files with multiple extensions are processed from last to the first one. With js.erb, the erb will process all the ruby content first, and then the javascript will be run. Your js.erb file should be saved in the same folder as the rest of your controller's views. This is an example :
{% highlight bash %}
app/views/users/
├── _my_partial.html.erb
├── edit.js.erb
├── show.html.erb {% endhighlight %}

Voila! When you click the link in your view the partial is loaded using Ajax.

Awesome.
