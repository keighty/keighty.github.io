---
layout: post
title: "exploring the stack"
date: 2015-03-25
comments: true
sharing: true
categories: [ruby, toolbox]
---

Another brilliant Rails troubleshooting technique I have recently added to my toolbox is [`pry-stack_explorer`](https://github.com/pry/pry-stack_explorer) as an add-on to [`pry`](https://github.com/pry/pry).

#### 1. Add `pry` and `pry-stack_explorer` to the development/test group of your gemfile:

{% codeblock lang:ruby %}
group :test, :development do
  gem 'pry'
  gem 'pry-stack_explorer'
end
{% endcodeblock %}

#### 2. Add a binding pry anywhere in your code
{% img /images/150325-show-stack/binding-pry.png %}

#### 3. Enter `show-stack` at the pry prompt
{% img /images/150325-show-stack/show-stack.png %}

#### 4. Enter `up` or `down` to explore the state of the stack
{% img /images/150325-show-stack/travel-the-stack.png %}

#### Other goodies
* Pass a string to `up` or `down` and `pry-stack_explorer` will jump to the first frame that matches:

{% codeblock lang:bash %}
Frame number: 0/108

From: /Users/keighty/Documents/projects/api_playground/app/models/user.rb @ line 4 User#full_name:

    3: def full_name
 => 4:   binding.pry
    5:   "#{first_name} #{last_name}"
    6: end

[6] pry(<User>)> up instrument

Frame number: 3/108
Frame type: method

From: /usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/activesupport-4.2.0/lib/active_support/notifications.rb @ line 166 ActiveSupport::Notifications.instrument:

    162: def instrument(name, payload = {})
    163:   if notifier.listening?(name)
    164:     instrumenter.instrument(name, payload) { yield payload if block_given? }
    165:   else
 => 166:     yield payload if block_given?
    167:   end
    168: end

[7] pry(ActiveSupport::Notifications)> down view

Frame number: 1/108
Frame type: method

From: /Users/keighty/Documents/projects/api_playground/app/views/greetings/hello.html.erb @ line 1 ActionView::CompiledTemplates#_app_views_greetings_hello_html_erb___3157151947187763552_70316982999600:

 => 1: <h1>Greetings, <%= @person.full_name %></h1>
    2: <p>This is a greetings view!</p>
{% endcodeblock %}

* Pass an integer to `up` or `down` to leap over frames

{% codeblock lang:bash %}
[8] pry(<Class:0x007fe7eb821330>>)> up 5

Frame number: 6/108
Frame type: block

From: /usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/actionview-4.2.0/lib/action_view/renderer/template_renderer.rb @ line 54 ActionView::TemplateRenderer#render_template:

    49: def render_template(template, layout_name = nil, locals = nil) #:nodoc:
    50:   view, locals = @view, locals || {}
    51: 
    52:   render_with_layout(layout_name, locals) do |layout|
    53:     instrument(:template, :identifier => template.identifier, :layout => layout.try(:virtual_path)) do
 => 54:       template.render(view, locals) { |*name| view._layout_for(*name) }
    55:     end
    56:   end
    57: end

[9] pry(<ActionView::TemplateRenderer>)> down 2

Frame number: 4/108
Frame type: method

From: /usr/local/var/rbenv/versions/2.1.5/lib/ruby/gems/2.1.0/gems/actionview-4.2.0/lib/action_view/template.rb @ line 333 ActionView::Template#instrument:

    331: def instrument(action, &block)
    332:   payload = { virtual_path: @virtual_path, identifier: @identifier }
 => 333:   ActiveSupport::Notifications.instrument("#{action}.action_view", payload, &block)
    334: end
{% endcodeblock %}

To learn even more awesome ways to use `pry` and `pry-stack_explorer`, check out the docs:

* [github.com/pry/pry](https://github.com/pry/pry).
* [github.com/pry/pry-stack_explorer](https://github.com/pry/pry-stack_explorer)