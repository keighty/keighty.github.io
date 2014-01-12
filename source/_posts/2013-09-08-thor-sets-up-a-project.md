---
layout: post
title: "Thor Sets Up A Project"
description: ""
category: automation
tags: []
---

A few weeks ago I posted about [using Thor to generate system-wide notes files with a standard format](http://www.katieleonard.ca/automation/2013/08/30/hammer-out-tasks-with-thor/). Since then, I have been doing a lot of smaller code challenges, and I wanted to set up a Thor task for generating a standard project file tree.

My Goal Tree:
{% highlight bash %}
project_name
├── lib
│   └── project_name.rb
├── spec
│   ├── project_name_spec.rb
│   └── spec_helper.rb
├── Gemfile
└── README.md{% endhighlight %}

I always begin with these files and this organization. A Gemfile and README are essential for setting up the environment and explaining the gist of the project. I am most comfortable with RSpec, and to get into the practice of TDD, setting up a testing environment right away is non-negotiable. All of these standard files contain some automatic content as well:

#####spec_helper.rb loads the library
{% highlight ruby %}
$LOAD_PATH.unshift(File.join(File.dirname(__FILE__), '..', 'lib'))
require 'rspec'
require 'project_name'
{% endhighlight %}

#####project_name_spec.rb sets up the first test
{% highlight ruby %}
require 'spec_helper'

describe ProjectName do
  before (:each) {  }
  subject {  }

  it "should pass"
end
{% endhighlight %}

#####Gemfile specifies the right gems for setting up the project
{% highlight ruby %}
ruby '2.0.0'
gem 'rspec'
{% endhighlight %}

There are other files that could have auto-generated content as well, like a Rakefile, or a config.ru, but this is a good start.

####Create a Thor task (rb_project.thor):
{% highlight ruby %}
#!/usr/bin/env ruby
require "rubygems"
require "thor"

class RbProject < Thor
  desc "init", "creates a ruby project with rspec"

  def init(title="new_project")
    Dir.mkdir(title)
    Dir.chdir(title)
    Dir.mkdir("spec")
    Dir.mkdir("lib")
    filename = "#{title}"

    open(File.new("README.md", "w"), "w") do |note|
      note.puts "# #{title}"
    end

    open(File.new("Gemfile", "w"), "w") do |my_gem|
      my_gem.puts "ruby '2.0.0'"
      my_gem.puts "gem 'rspec'"
    end

    open(File.new("spec/spec_helper.rb", "w"), "w") do |spec|
      spec.puts "$LOAD_PATH.unshift(File.join(File.dirname(__FILE__), '..', 'lib'))"
      spec.puts "require 'rspec'"
      spec.puts "require '#{filename}'"
    end

    open(File.new("spec/#{filename}_spec.rb", "w"), "w") do |spec|
      spec.puts "require 'spec_helper'"
      spec.puts ""
      spec.puts "describe #{camelize(filename)} do"
      spec.puts "  before (:each) {  }"
      spec.puts "  xit 'should pass'"
      spec.puts "end"
    end
  end

  private
    def camelize(snake)
      title = snake.split('_').each do |word|
        word.capitalize!
      end
      title.join('')
    end
end {% endhighlight %}

I created a private method camelize in order to change the snake_case project name into a CamelCase class name.

As before, I install my thor task system wide:
{% highlight bash %}
$ thor install rb_project.thor {% endhighlight %}

And now my project generation task can be run from any directory, anywhere on my system:
{% highlight bash %}
$ thor list
note
----
thor note:create  # creates a notes file in markdown

rb_project
----------
thor rb_project:init  # creates a ruby project with rspec

$ thor rb_project:init test_project
$ tree test_project/
test_project/
├── lib
│   └── test_project.rb
├── spec
│   ├── spec_helper.rb
│   └── test_project_spec.rb
├── Gemfile
├── README.md

2 directories, 6 files
{% endhighlight %}

Awesome.


{% highlight bash %}{% endhighlight %}
{% highlight bash %}{% endhighlight %}

