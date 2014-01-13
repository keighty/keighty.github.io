---
layout: post
title: "Parcel Parser"
comments: true
description: ""
category: eh
tags: []
---

On the last episode, I described how to label chunks of code for processing through a parser. Strings are labeled as 'STRING', class definitions as 'A' + 'CONSTANT', etc.

##What is a parser?
A parser separates and analyzes a piece of text according to a set of rules specified by a formal grammar. The analysis is performed by assembling the tokenized code into an Abstract Syntax Tree (AST) - a tree of nodes that represent what the code means to the language. The AST evaluates the nodes in a similar manner to order of operations in math: each token is placed on the evaluation tree, and expressions are evaluated by reducing each branch in order.

The parser itself is can be written by hand, but I am using RACC: an LALR (Look Ahead, Left to right, Reverse) parser written by [tenderlove](https://github.com/tenderlove/racc) to generate Ruby programs. How do we specify a grammar that RACC will understand?

Each rule is formatted in the following way:
{% highlight bash linenos%}
RuleName:
  OtherRule TOKEN AnotherRule    { code to run }
| OtherRule                      { ... }
; {% endhighlight %}

It is similar to an if/else statement that captures all possible expressions beginning with the most specific (ie: an expression with TWO rules matches line 2) to more general (all other single expressions are captured on line 3). When a token matches a rule, the code in the attached code block is run.

The code blocks correspond to instructions on how to treat matching tokens. For example, the grammar should specify what happens when the code contains a class definition, labeled with 'A' + 'CONSTANT'.

{% highlight ruby %}
...
# Class definition
Class:
  A CONSTANT Block { result = ClassNode.new(val[1], val[2]) }
;
... {% endhighlight %}

When the parser catches a class token it will create a new ClassNode with the class name (CONSTANT) and the block as arguments. The val\[\] array refers to the grammar rule \[A, CONSTANT, Block\].

Some tokens are parsed very close to AS IS:

{% highlight ruby %}
Literal:
  NUMBER { result = LiteralNode.new(val[0]) }
| STRING { result = LiteralNode.new(val[0]) }
| TRUE   { result = LiteralNode.new(true) }
| FALSE  { result = LiteralNode.new(false) }
| NIL    { result = LiteralNode.new(nil) }
; {% endhighlight %}

Each literal triggers the creation of a new LiteralNode with its value as the only argument.

The Nodes are outlined in a Nodes class, where the rules of evaluation are defined.

{% highlight ruby %}
...
class ClassNode
  def initialize(name, body)
    @name = name
    @body = body
  end

  def eval(context)
    eh_class = CanadianClass.new
    context[@name] = eh_class
    @body.eval(Context.new(eh_class, eh_class))
    eh_class
  end
end
...
{% endhighlight %}

A ClassNode is initialized with two params (recall from the grammar: ClassNode.new(val\[1\], val\[2\])), the class name and its code block. The context is like scope - it can hold modules, classes, methods, attributes, aliases, requires, and includes. Classes, modules, and files are all Contexts (definition from [Rdocs](http://ruby-doc.org/stdlib-1.8.6/libdoc/rdoc/rdoc/RDoc/Context.html)). Evaluation of a ClassNode begins by assigning the class to a context and evaluating the code block, which adds more contexts. In this way, a tree structure is formed - an AST - which does all the interpreting work for the new language.

When the grammar and node definitions are complete, RACC will generate a parser:
{% highlight ruby %}
$ racc -vo parser.rb grammar.y {% endhighlight %}

The parser is a relatively obtuse set of methods and state transition tables, and when you run code through the parser you get an AST that follows the grammar you have defined.

So, now I have a lexer and a parser, but I still can't run my code. I need to define a runner class that will put all these parts together, but first, I need a break.

Awesome.

