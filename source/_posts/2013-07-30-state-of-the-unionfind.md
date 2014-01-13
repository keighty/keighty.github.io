---
layout: post
title: "State of the UnionFind"
comments: true
description: ""
category: ruby
tags: []
---

I was scraping the rust off my data-structures synapses last week, and found that there was still some functionality underneath. UnionFind is one of those sneaky algorithms that crops up in non-linear arrangements of data, such as computer or social networks. Specifically, it keeps track of sets of connected elements. The API consists of a union(p, q), which connects the two elements p and q, and a find(p, q), which determines if p is in the same set as q.

I learned about this algorithm through [Coursera](https://www.coursera.org/), and their class [Algorithms Part 1](https://www.coursera.org/course/algs4partI) with Kevin Wayne and Robert Sedgewick. The focus of this course was not only on implementation of algorithms, but also optimization.

#Union Find
A simple model for connectivity is 10 objects numbered 1 through 0:

0 1 2 3 4 5 6 7 8 9

Making random connections will produce disjointed sets of objects:

0 1 { 2 3 9 } { 5 6 } 7 { 4 8 }

A find(p, q) will determine if p and q are in the same set:

* find(0, 8) => false
* find(2, 9) => true

A union(p, q) will put p and q in the same set:

* union(1, 6) => 0 { 2 3 9 } { 1 5 6 } 7 { 4 8 }
* union(2, 4) => 0 1 { 5 6 } 7 { 2 3 9 4 8 }

Using an array to keep track of the values, we can walk through what this implementation would look like:

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
    <th></th>
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
    <td> | Changes all values that match array[4] to match array[9]</td>
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
    <td class="highlight">9</td>
    <td>0</td>
    <td> | Changes all values that match array[2] to match array[4]</td>
  </tr>
  <tr>
    <th>union(4,7)</th>
    <td>1</td>
    <td class="highlight">7</td>
    <td>3</td>
    <td class="highlight">7</td>
    <td>5</td>
    <td>6</td>
    <td class="highlight">7</td>
    <td>8</td>
    <td class="highlight">7</td>
    <td>0</td>
    <td> | Changes all values that match array[4] to match array[7]</td>
  </tr>
</table>


Using the java api provided by Kevin Wayne and Robert Sedgewick:

{% highlight java %}
public class UF
----------------
public UF(int N)
public int find(int p, int q)
public void union(int p, int q) {% endhighlight %}

I adapted the basic union find in ruby:

{% highlight ruby linenos%}
class QuickFind

  attr_accessor :find_array

  def initialize(size)
    @find_array = Array.new(size){ |index| index }
  end

  def find(p, q)
    location(p) == location(q)
  end

  def union(p, q)
    @find_array = create_union(p, q)
  end

  private

    def location(index)
      raise NotAnElement if @find_array[index].nil?
      return @find_array[index]
    end

    def create_union(p, q)
      target = location(p)
      goal = location(q)
      temp_array = @find_array.map do |element|
        element == target ? goal : element
      end
    end
end

class NotAnElement < Exception
end
{% endhighlight %}

Sets are arranged as a flat array of elements which all share the same value: they are all wearing the same t-shirt. When we connect elements from different sets, we must make sure that every element in the first set changes its t-shirt to match the elements in the other set. This is a time-consuming process, and uses a lot of t-shirts.

This implementation is called QuickFind because in an array of 1,000,000 entries, finding if two elements are connected is easy (compare the t-shirt at index p with the t-shirt at index q), but connecting them requires inspection of every element in the array in order (check everyone's t-shirts) to make sure all the previous connections remain in place.

##Benchmark
To compare efficiency of this and future UnionFind implementations, I built a basic benchmark that performs a set number of union and find operations and measures the time to completion:
{% highlight ruby %}
require 'benchmark'
@size = 10000

def testUnit(object)
  @size.times do
    object.union(rand(@size), rand(@size))
    object.find(rand(@size), rand(@size))
  end
end

Benchmark.bm do |x|
  x.report { testUnit(QuickFind.new(@size)) }
end
{% endhighlight %}

Running the benchmark on QuickFind:

{% highlight bash %}
$ ruby lib/benchmark.rb
=> Array size = 100
       user     system      total        real
   0.000000   0.000000   0.000000 (  0.002745)
$ ruby lib/benchmark.rb
=> Array size = 1000
       user     system      total        real
   0.140000   0.000000   0.140000 (  0.137957)
$ ruby lib/benchmark.rb
=> Array size = 10000
       user     system      total        real
  13.790000   0.020000  13.810000 ( 13.866528){% endhighlight %}

You can see from these measurements that increasing the size of the array by a factor of 10 each time comes with a logarithmic increase in processing time. There is definitely room for optimization, so stay tuned for more refinements. In the meantime,

##Fun Rubyisms
Array initialization is a snap in ruby! Compare the initialization line in the QuickFind algorithm above with the equivalent java implementation:
{% highlight ruby %}
@find_array = Array.new(size){ |index| index }
{% endhighlight %}
versus
{% highlight java %}
...
int[] arry = new int[size]
for (int i = 0; i < size; i++) {
    id[i] = i;
}
{% endhighlight %}

Both result in an array of the specified size, but ruby is slightly less sprawling.

Also, check out lines 27 - 29
{% highlight ruby %}
temp_array = @find_array.map do |element|
  element == target ? goal : element
end
{% endhighlight %}

Using map() eliminates the need for a separate array declaration. It is the equivalent of:
{% highlight ruby %}
temp_array = []
@find_array.each do |element|
  temp_array << element == target ? goal : element
end
return temp_array {% endhighlight %}

and is certainly more readable than the equivalent in java:
{% highlight java %}
int pid = id[p];
for (int i = 0; i < id.length; i++)
    if (id[i] == pid) id[i] = id[q];
{% endhighlight %}

Awesome