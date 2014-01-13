---
layout: post
title: "Flex Your Lexer"
comments: true
description: ""
category: eh
tags: []
---

A Lexer is a ruby class with a single method: tokenize(). Its purpose is to label each chunk of code with a particular token. Strings are labeled as strings, numbers as numbers, and class names as constants. For example,

{% highlight ruby %}
Lexer.new.tokenize("string")
# =>[[:STRING, "string"]]
Lexer.new.tokenize("True")
# =>[[:CONSTANT, "True"]]
Lexer.new.tokenize("a Car")
# =>[[:A, 'a'], [:CONSTANT, 'Car']])
Lexer.new.tokenize("+")
# =>[["+", "+"]]
{% endhighlight %}

"Lexing" is accomplished using regular expressions. The lexer treats the text string of your code like a shish kabob - it slides recognizable chunks off the skewer one at a time and categorizes them (onion, pepper, method definition, etc).

{% highlight ruby %}
class Lexer
  KEYWORDS = ['a', 'can', 'if', 'else', 'while', 'true', 'false', 'nil']

  def tokenize(code)
    code.chomp!
    i = 0
    tokens = []

    while i < code.size
      chunk = code[i..-1]

      # checks keywords
      if identifier = chunk[/\A(eh\?)/, 1]
        tokens << ['}', '}']
        i += identifier.size

      elsif identifier = chunk[/\A([a-z]\w*)/, 1]
        if KEYWORDS.include?(identifier)
          tokens << [identifier.upcase.to_sym, identifier]
        else
          tokens << [:IDENTIFIER, identifier]
        end
        i += identifier.size
...{% endhighlight %}
In this example lexer I have defined my list of keywords, removed the trailing newline, and begun peeling identifiable chunks off the code example.
* If the code chunk matches an 'eh?' it will add a closing brace to the token array.
* If the code chunk matches a KEYWORD it will be upcased and labeled as a keyword and added to the token array.
* If the code chunk matches any word of lowercase letters it will be labeled as an IDENTIFIER and added to the token array.

I continued defining regex rules until I was able to completely tokenize a class definition:

{% highlight ruby %}
code = <<-EOS
a Canadian
  can curl
    if skip:
      say 'Hurry!'
    eh?
  eh?
eh?
EOS
...
Lexer.new.tokenize(code)
# => [[:A, "a"], [:CONSTANT, "Canadian"], [:CAN, "can"], [:IDENTIFIER, "curl"], [:IF, "if"], [:IDENTIFIER, "skip"], ["{", "{"], [:IDENTIFIER, "say"], [:STRING, "Hurry!"], ["}", "}"], ["}", "}"], ["}", "}"]]{% endhighlight %}

Now that I have an array of tokens, it is time to write my grammar laws that will tell the parser what to do with them.

Awesome