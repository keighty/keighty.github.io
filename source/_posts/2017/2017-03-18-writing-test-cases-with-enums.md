---
layout: post
title: "writing test cases in java with enums"
date: 2017-03-18
sharing: true
categories: [java]
---

I recently discovered some new (to me) features of enums, and so naturally I am now using them for everything. One of these new-to-me features is using a constructor to give enum members properties and behaviour.<!--more-->

For example, if I were making a list of colours, a simple enum would look like this:

```java
enum Colors {
    RED,
    BLUE,
    YELLOW;
}
```

Everywhere in my code where I used to use the string `"red"`, I could replace with a less-spelling-error-prone-and-IDE-friendly `Colors.RED`. If I want to iterate over all the colors in my collection, I can use the `values` method:

```java
...
public void loopColorEnum() {
    for (Color color : Color.values()) {
        System.out.println(color);
    }
}
...

// console output:
// RED
// BLUE
// YELLOW
```

If I wanted to store the hex value for each colour, I can add a constructor and provide arguments for each member of the enum:

```java
enum Color {
    RED("#FF0000"),
    BLUE("#0000FF"),
    YELLOW("#FFFF00");

    final String hex;
    Color(String hexCode) {
        this.hex = hexCode;
    }
}
```

Now I can access the hex property on all of my colours:

```java
...
    public void loopColorEnum() {
        for (Color color : Color.values()) {
            System.out.println(color.hex);
        }
    }
...

// console output:
// #FF0000
// #0000FF
// #FFFF00
```

Finally, if I wanted to create a custom output for my enum that included the hex value, I can add a `toString()` function:

```java
enum Color {
    RED("#FF0000"),
    BLUE("#0000FF"),
    YELLOW("#FFFF00");

    String hex;
    Color(String hexCode) {
        this.hex = hexCode;
    }

    public String toString() {
        return String.format("Color: %s (Hex: %s)", super.toString(), hex);
    }
}

...
    public void loopColorEnum() {
        for (Color color : Color.values()) {
            System.out.println(color);
        }
    }
...

// console output:
// Color: RED (Hex: #FF0000)
// Color: BLUE (Hex: #0000FF)
// Color: YELLOW (Hex: #FFFF00)
```

Pretty cool! Pair a constructor with the `values()` iterable, and you have yourself a handy-dandy test framework for repetitive but important tests. For example, I was re-visiting an old exercise from a data structures class and wanted to write a bunch of test cases without the repetitive boilerplate code for each one -- so I built an enum:

```java
public class CalculatorTest extends InfixCalculator {

    private enum Expression {
        ADDITION("1+2+3", 6),
        SUBTRACTION("9-4", 5),
        MULTIPLICATION("5*4*2", 40),
        DIVISION("9/3", 3),
        ADDITION_WITH_SPACES("5 + 7", 12),
        MULTI_DIGITS("12 + 3 - 5", 10);

        final String expression;
        final int result;
        Expression(String expression, int result) {
            this.expression = expression;
            this.result = result;
        }
    }

    @Test
    public void test_solves_an_expression() {
        Calculator calc = new Calculator();
        for (Expression exp : Expression.values()) {
            assertEquals("for expression " + exp.expression,
                    exp.result,
                    calc.calculate(exp.expression));
        }
    }
}
```

When I create a new test case, I just add another member to the enum, and provide the string input and the expected result. Reducing the boilerplate made developing around edge cases far more wieldy, and I spent more time writing the actual code of the Calculator than I did creating tests.

Adding a constructor, methods and properties to an enum makes it looks suspiciously like a class. In fact, that is how enums are implemented under the hood: they are classes with _benefits_, such as:

* retrieve the constant value with `valueOf`,
* guaranteed singletons,
* clarity in switch statements, and
* built in ordering of members -- an enum can act like an array, and return the order of a member the `ordinal()` method

Sure, you could accomplish most of these using a class with `public static final` constants and a few hand-rolled methods, but you will impress your neighbours more with a well crafted enum.

Awesome.
