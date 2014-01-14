---
layout: post
title: "ARGF all you want"
date: 2013-09-05 21:29:03 -0800
comments: true
description: ""
category: ruby
tags: [I/O]
---

Many of the fun, junior code challenges I have encountered deal a lot with input/output of data, and I found a great ruby feature that provides flexibility at the command line.
<!--more-->
###Data Input
There are a lot of ways to treat data at the command line, but the one I was most familiar with is passing in a filename as an argument:

{% highlight bash linenos%}
$ ruby example_script.rb file1.csv file2.csv ...
$ ruby example_script.rb file1.csv | more
$ ruby example_script.rb file1.csv > output.txt{% endhighlight %}

###Filename Input
Using ARGV, we can access the list of filenames provided at the command line. ARGV is an array containing all the information that follows the command. For example:
{% highlight bash %}
$ example_command apple banana orange
# ARGV = ["apple", "banana", "orange"]{% endhighlight %}

One can process each filename in ARGV using regular array methods:

{% highlight ruby %}
ARGV.each do |arg|
  process_string(File.read(arg))
end {% endhighlight %}

File.read(arg) reads the file into a "\n" delimited string, which can be processed further. If there are no arguments, ARGV is an empty array.

###Piped Input
Say that we want to enter data directly into a program rather than use a fixed filename at the command line -- a useful technique for when we want data to flow out of one process and into another:
{% highlight bash %}
$ cat file1.csv | ruby example_script.rb
$ cat *.csv | ruby example_script.rb
$ ruby example_script.rb < file1.csv {% endhighlight %}

Using the pipe, "|" or "<", we can push a string of data from one process into another, but what happens to our program when we try to do this?
{% highlight bash %}
$ example.csv | ruby example_script.rb
TypeError: can`t convert nil into String {% endhighlight %}

We get a TypeError! Recall that command line arguments are stored in ARGV, but we are not providing any command line arguments. ARGV is an empty array, and we are attempting to open a file from ARGV\[0\] = nil. Shame on us! How do we access data that is piped in as well as data that is appended to the command line? Do we have to check for the source of the data before we can treat it? Turns out, we DON'T.

###The ARGF Solution
Ruby has a nifty interface for handling data input, regardless of whether it arrives as a command line argument or from another data source. We can replace the code we wrote earlier with a single line:

{% highlight ruby%}
process_string(ARGF.read){% endhighlight %}

If ARGV is not empty, ARGF will assume it is an array of filenames and will treat them accordingly. If ARGV is empty, it will read from $stdin to get the data passed in via the pipe. One caveat is that you can't read from both ARGV and the pipe: if ARGV != \[  \], $stdin is ignored.

Awesome.