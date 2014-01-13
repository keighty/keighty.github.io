---
layout: post
title: "Hammer-out Tasks with Thor"
comments: true
description: ""
category: ruby
tags: []
---

A few months ago I gave a small lightning talk to my code school class about automating tasks using [Rake](http://rake.rubyforge.org/). Before I learned ruby I used bash scripts to accomplish similar things, but was often bogged down in learning the syntax of string-processing essentials like awk or sed. I wished for the simplicity of Ruby, and thought that Rake was the bees-knees. After my talk, [Chuck](chuckvose.com), my PCS mentor, put together a repo of [Thor](http://whatisthor.com/) tasks that accomplished the same tasks but could be installed system wide.
<!--more-->
###[Thor](http://whatisthor.com/) is my new hero
Unlike Rake tasks, which are confined to the directory containing the Rakefile, Thor tasks can be installed on your system and called from anywhere. My note files are all formatted in a particular way. Before Thor, I used a rake task:

{% highlight bash %}
$ cd notesDirWithRakefile
$ rake post title="new post title"
$ Creating new post: "./130830_new_post_title"{% endhighlight %}

The Rake task auto-formats the filename, replacing spaces with underscores, and prepends today's date so that they will sort in the order they are made. This worked great for when I wanted to keep notes in that one folder, but later I wanted to be able to keep notes on a particular project. I would either have to copy the Rakefile (two places to update if I made any changes), or create a symbolic link (which would break if I ever moved my Rakefile). Neither of these options seemed to be the most practical. Enter Thor:

Creating the Thor task is as easy as pie:
{% highlight ruby %}
require 'thor'

class Note < Thor
  desc "create", "creates a notes file in markdown"
  def create(title="new note")
    slug = title.downcase.strip.gsub(' ', '_').gsub(/[^\w-]/, '')
    date = Time.parse(Time.now).strftime('%y%m%d')
    filename = File.join("./", "#{date}_#{slug}.md")

    if File.exist?(filename)
      abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
    end

    puts "Creating new post: #{filename}"
    open(filename, 'w') do |post|
      post.puts "# #{title.gsub(/-/,' ').capitalize}"
    end
  end
end {% endhighlight %}

Listing all Thor tasks is easy:
{% highlight bash %}
$ thor list
note
----
thor note:create  # creates a notes file in markdown {% endhighlight %}

Installing a Thor task for system use is easy:
{% highlight bash %}
$ thor install notes.thor
...
Do you wish to continue [y/N]? y
Please specify a name for notes.thor in the system repository [notes.thor]: note
Storing thor file in your system repository {% endhighlight %}

Uninstalling a Thor task is easy:
{% highlight bash %}
$ thor uninstall note
Uninstalling note.
Done. {% endhighlight %}

With my Thor notes task installed system-wide, I can create a note page in any directory, for any reason, without copying code.

Awesome.