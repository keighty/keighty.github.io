---
layout: post
title: "Ruby Execution"
description: ""
category: ruby
tags: []
---

I have started including an executable in my project setup. Many of the code challenges I have been practicing lately have included file I/O, and while TDD and code exercising with RSpec is still my main process, developing a stand-alone fully-functional project requires something more.

###Blocks Code Challenge
I found the blocks code challenge on the [UVa code competition website](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=3&page=show_problem&problem=37). To sum up, you have to write a program that will parse and carry out a series of commands for stacking blocks.

1. move a onto b
2. move a over b
3. pile a onto b
4. pile a over b
5. quit

The commands will be provided in an input file, and an output is specified. Fun problem, right? So, I start my project folder using my [thor task](http://www.katieleonard.ca/automation/2013/09/08/thor-sets-up-a-project/), and work awhile adding logic and tests and data as appropriate. My file tree finishes like this:
{% highlight bash %}
blocks
├── data
│   ├── input.txt
│   └── output.txt
├── lib
│   └── blocks.rb
├── spec
│   ├── blocks_spec.rb
│   └── spec_helper.rb
├── Gemfile
└── README.md{% endhighlight %}

So, in order to use my script, I would have to call it from the command line:
{% highlight bash %}
$ ruby lib/blocks.rb data/input.txt
#=> or
$ data/input.txt | ruby lib/blocks.rb{% endhighlight %}

This is an extremely verbose way to deliver a final product, and I would much rather call $ blocks &lt;file_input&gt;. It turns out that making an executable is easy and elegant, just like everything else in ruby. You just have to declare the ruby environment, include a few notes on usage, load the file tree, and call the class:
{% highlight ruby %}
#!/usr/bin/env ruby
# blocks
# 10-Sep-2013
#
# Usage:
# ./blocks data/input.txt
#
$LOAD_PATH.unshift(File.join(File.dirname(__FILE__), 'lib'))
require 'blocks'
Blocks.new.process_input{% endhighlight %}

Placing this code in a non-extension file, like 'blocks', in the main directory, I can make it an executable by changing the file permissions:
{% highlight bash %}
$ chmod +x ./blocks{% endhighlight %}

And I can call it like any other executable:
{% highlight bash %}
$ ./blocks data/input.txt{% endhighlight %}

I liked this solution so much, I added it to my thor project setup! Oh, and if you are interested in seeing my solution for the blocks problem, checkout my [github repo](https://github.com/keighty/datastructures/tree/master/ruby/blocks)

Awesome.
