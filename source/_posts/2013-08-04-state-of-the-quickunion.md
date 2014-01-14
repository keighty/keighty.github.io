---
layout: post
title: "State of the QuickUnion"
date: 2013-08-04 21:29:03 -0800
comments: true
description: ""
category: ruby
tags: []
---

In my last look at the UnionFind algorithm I made a quick-find implementation -- finding if two elements were connected was fast but connecting two elements was slow. The union operation is a good candidate for optimization.
<!--more-->
#Quick Union
One way to optimize the union operation is to reduce the number of array accesses required.

In QuickFind, the sets are arranged in a flat array of elements which all share the same value: they are all wearing the same t-shirt. When we connect elements from different sets, we must make sure that every element in the first set changes its t-shirt to match the elements in the other set. This is a time-consuming process, and uses a lot of t-shirts.

What if only one element had to change its t-shirt? If the set had a single element in charge - a root - then joining elements from different sets would require identifying the t-shirt of the one in charge, and then changing to that element's t-shirt.

Using an array instead of t-shirts to keep track of the values, we can walk through what this implementation would look like:

<table class="table table-bordered">
  <tr>
    <th>Index</th>
    <td>1</td>
    <td>2</td>
    <td>3</td>
    <td>4</td>
    <td>5</td>
    <td>6</td>
    <td>7</td>
    <td>8</td>
    <td>9</td>
    <td>0</td>
    <th></th>
  </tr>
  <tr>
    <th>Value</th>
    <td>1</td>
    <td>2</td>
    <td>3</td>
    <td>4</td>
    <td>5</td>
    <td>6</td>
    <td>7</td>
    <td>8</td>
    <td>9</td>
    <td>0</td>
    <td> | Each element begins as the root of its own set</td>
  </tr>
  <tr>
    <th>union(4,9)</th>
    <td>1</td>
    <td>2</td>
    <td>3</td>
    <td class="highlight">9</td>
    <td>5</td>
    <td>6</td>
    <td>7</td>
    <td>8</td>
    <td class="highlight">9</td>
    <td>0</td>
    <td> | root(9) = 9, root(4) = 4, change array[4] = 9</td>
  </tr>
  <tr>
    <th>union(2,4)</th>
    <td>1</td>
    <td class="highlight">9</td>
    <td>3</td>
    <td class="highlight">9</td>
    <td>5</td>
    <td>6</td>
    <td>7</td>
    <td>8</td>
    <td>9</td>
    <td>0</td>
    <td> | root(4) = 9, root(2) = 2, change array[2] = 9</td>
  </tr>
  <tr>
    <th>union(4,7)</th>
    <td>1</td>
    <td>9</td>
    <td>3</td>
    <td  class="highlight">9</td>
    <td>5</td>
    <td>6</td>
    <td  class="highlight">7</td>
    <td>8</td>
    <td>7</td>
    <td>0</td>
    <td> | root(7) = 7, root(4) = 9, change array[9] = 7</td>
  </tr>
  <tr>
    <th>find(2,7)</th>
    <td>1</td>
    <td class="highlight">9</td>
    <td>3</td>
    <td>9</td>
    <td>5</td>
    <td>6</td>
    <td class="highlight">7</td>
    <td>8</td>
    <td class="highlight">7</td>
    <td>0</td>
    <td> | root(7) = 7, root(2) = 7. The two are connected </td>
  </tr>
</table>

##QuickUnion
Again following the basic API:
{% highlight java %}
public class UF
---------------
public UF(int N)
public int find(int p, int q)
public void union(int p, int q){% endhighlight %}

I adapted the QuickUnion in ruby:
{% highlight ruby linenos %}
class QuickUnion
  def initialize(size)
    @find_array = Array.new(size){ |index| index }
  end

  # check if p and q have the same root
  def find(p, q)
    verify(p, q)
    root(p) == root(q)
  end

  # set id of p's root to the id of q's root
  def union(p, q)
    verify(p, q)
    @find_array[root(p)] = root(q)
  end

  private
    def root(index)
      # get root until root(index) == index
      if @find_array[index] == index
        return index
      else
        root(@find_array[index])
      end
    end

    def verify(p, q)
      raise NotAnElement if @find_array[p].nil?
      raise NotAnElement if @find_array[q].nil?
    end
end

class NotAnElement < Exception
end
{% endhighlight %}

This implementation is a QuickUnion because in an array of 1,000,000 entries, connecting element p to element q is easy: you only have to find the root of p, and then change q to match (one root() and one array access). Finding if p and q are connected takes a little bit longer, because you have to perform two root operations: one for p and one for q, but it is still faster than accessing every element in the array.

##Benchmark
{% highlight bash %}
$ ruby lib/benchmark.rb
=> Array Size: 100
       user     system      total        real
   0.000000   0.000000   0.000000 (  0.001865)
   0.000000   0.000000   0.000000 (  0.000377)
$ ruby lib/benchmark.rb
=> Array Size: 1000
       user     system      total        real
   0.120000   0.010000   0.130000 (  0.124929)
   0.010000   0.000000   0.010000 (  0.008825)
$ ruby lib/benchmark.rb
=> Array Size: 10000
       user     system      total        real
  10.120000   0.030000  10.150000 ( 10.146124)
   0.520000   0.000000   0.520000 (  0.520377){% endhighlight %}

Comparing QuickFind (top line) with QuickUnion(bottom line), we can actually measure how well this small optimization of the union operation has increased performance with larger datasets.

Awesome.