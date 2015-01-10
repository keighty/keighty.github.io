---
layout: post
title: "compilers -- an intro"
date: 2014-11-08
comments: true
categories: [cs, languages]
---

Last year, I was wrapping up a stint at code school, and I picked up the challenge of [building a programming language](http://katieleonard.ca/blog/2013/canadian-flair/). It was a very large stretch-goal, and I didn't complete the project, but I learned a lot about how languages are designed and built. Recently, my mentor highlighted a [Compilers course offered through Stanford Online](http://online.stanford.edu/course/compilers-0), and suggested that it may be time to level-up my understanding of languages once again. Since I did not reach the compiler phase for `eh?`, perhaps this is an opportunity to do so.

### Review of compilers

A compiler is different from an interpreter. An interpreter takes both the data and the program and produces output. A compiler takes only the program and produces an executable. Fun fact: I learned just recently that javascript is actually a "compiled" language -- the javascript engine uses just-in-time compilation as it is executed.

The grandfather of all compiled languages was FORTRAN, which was developed to translate formulas into a machine-readable language. Like many software projects, the initial completion date was wildly optimistic -- it took 7 years longer than their original estimate -- but they developed a 5-stage pattern of processing that all later compilers would follow.

### 1. Lexical analysis
This first step is about word and symbol recognition: breaking a stream of characters into recognizable chunks. For example a method definition:

```
# Ruby
def my_method; end

// Java
public static void main() { }

# eh
can curl; eh?
```

Recognizing white space, punctuation, and words is the first step in translating characters into actions that can be performed. The words that are recognized are usually converted into "tokens", for passing in to the next stage of processing, which is why this step is occasionally called Tokenizing.

### 2. Parsing
After the character stream has been tokenized, doing pattern recognition on the sequence of tokens is called parsing. Returning briefly to the example of a method definition, ` def my_method; end `, the lexer would recognize `def` as a key word and `my_method` as a variable, but they are grouped together by the parser as the beginning of a method block.

### 3. Semantic analysis
Compilers can only do a very limited amount of semantic analysis, usually limited to catching inconsistencies (such as redeclaring a variable) and ambiguities (such as type mismatches). "A panda bear walks into a bar and eats, shoots and leaves", can be tokenized into component parts (verbs, nouns, articles), as well as into phrases (subject, predicate), but determining the exact meaning of the predicate requires deeper understanding of the subject. Machines do not perform well with ambiguous grammar, and developing an unambiguous language syntax is a difficult task.

### 4. Optimization
Optimization is where most modern language compilers spend the majority of their resources, modifying programs so that they reduce the demand for resources like memory allocation, or garbage collection. For example, the java compiler will "unroll" all the for loops so that they run more efficiently. Another strategy is [peephole](http://en.wikipedia.org/wiki/Optimizing_compiler) optimization, where the compiler checks out adjacent code to see if any assignments could be reduced or replaced by simple math.

`x = y * 2` might be reduced to ` x = y + y`.

`z = 0; x = y * z` might be reduced to `x = 0`, but there is a gotcha hidden there -- `y * 0 = 0` is only true for integers. It results in `NaN` for floating point numbers.

### 5. Code generation
Code generation is the final translation of the program into an executable. The written language has been cut into tokens and parsed into phrases. It has been examined for ambiguities and inconsistencies, and optimized wherever possible. The instructions for the program are arranged neatly and must now be evaluated and scheduled. The final product is an executable program that can be run against any data of interest.

This 5 stage pattern is followed by almost all compilers. Where they vary is in their proportions. FORTRAN spent a balanced amount of time in each of the 5 phases, while modern compilers have a much longer optimization phase than lexing or parsing.
