---
layout: post
title: "thread safety"
date: 2017-02-04
sharing: true
categories: [java]
---

While wading into the wide world of high throughput production Java, I have been enjoying guidance from "Java Concurrency in Practice" by Brian Goetz, as well as my friend and coworker, David Copeland. In a recent talk, David boiled down the range of concurrency problems to three main issues: atomicity, visibility, and ordering conflicts.<!--more-->

### Atomicity
An atomic operation is one that executes in a single operation. A variable assignment (`int x = 2;`) is an example of an atomic operation (mostly* -- see footnote). Incrementing a variable, on the other hand, is NOT an atomic operation.

```java
public class NonAtomicCounter {
  private int count = 0;

  public int getCount() { return count; }
  public void doWork() { count++; }
}
```

While `count++` _appears_ to be a single action it is actually three:

* read count,
* modify count, and
* write count.

If ThreadA and ThreadB share a NonAtomicCounter and both call `doWork()`, ThreadA can read the value of count while ThreadB is modifying it, making `count` vulnerable to lost updates.

> Be suspicious of any shared state that is involved in _read-modify-write_ or _test-then-act_ sequences of actions.

### Visibility
Once a thread has read a value, there is no guarantee that it will ever check to see if another thread has modified that value.

```java
public class Main {
    static boolean stop;

    public static void main(String[] args) throws InterruptedException {

        new Thread(() -> {
            while (! stop) {
                System.out.println("still working");
            }
        }).start();

        new Thread(() -> { stop = true; }).start();
    }
}
```

Each thread can cache a local copy of `stop`, which means they will read the value once and then never check to see if it has been changed.

> Be suspicious of loops that are gated by a variable that is visible to other threads.

### Ordering
The JVM has a just-in-time compiler that optimizes execution at runtime. The memory model guarantees deterministic behaviour _within_ a thread, but makes no promises about how instructions are ordered in the meantime.

```java
public class Main {
    static int A;
    static int B;

    public static void main(String[] args) throws InterruptedException {

        Thread t1 = new Thread(() -> {
            int localA = A;
            B = 1;
            System.out.println("local A: " + localA);
        });

        Thread t2 = new Thread(() -> {
            int localB = B;
            A = 2;
            System.out.println("local B: " + localB);
        });

        t1.start();
        t2.start();
    }
}

/* POSSIBLE LOCAL VALUES
* localA: 0, localB: 0
* -------------
* localA: 0, localB: 1
* --------------
* localA: 2, localB: 1 => THIS SHOULD BE IMPOSSIBLE, RIGHT?
* --------------
* localA: 2, localB: 0 => WAT?
*/
```

How is it possible that t1 sees that A = 2? From [the docs](https://docs.oracle.com/javase/specs/jls/se7/html/jls-17.html#jls-17.4):

> "The semantics of the Java programming language allow compilers and microprocessors to perform optimizations that can interact with incorrectly synchronized code in ways that can produce behaviors that seem paradoxical.""

What this means, is that even though the instructions are written in such a way that localA should not be able to see the reassignment of A, the compiler can reorder instructions to optimize evaluation. As long as the result of the computation is deterministic **within** a thread, the compiler is free to change the execution order, inline assignments, simplify algebra, etc.

In the above example,

```java
int localB = B;
A = 2;
```

is the same as
```java
A = 2;
int localB = B;
```

When the instructions for thread1 and thread2 are interleaved, thread1 will occasionally see `A = 2`.

> Be suspicious of operations involving multiple variables and ordering requirements.

### Thread Safety Analysis Guidelines

If you are dealing with a thread safety issue, ask these three questions:

3. Do your threads share mutable state? Look for public getters and setters, or methods that return references to state rather than copies or primitive values.
2. Do your threads share multi-variable states (invariants)? Can one component  change independently from others when they should always be treated as a single unit?
1. For any publicly mutable state, is it an atomicity, visibility, or ordering issue?

### Solutions for thread safety issues
The Java language has a few solutions to offer:

#### The `volatile` keyword
Adding the keyword `volatile` to a variable declaration tells the JVM not to use a cached value for any read operations. This keyword will ensure that writes to a variable are always visible to other threads, but will not fix atomicity or ordering issues.

#### The `synchronized` keyword
Using a `synchronized` lock around a variable will address all three issues, at the cost of concurrency. If only one thread at a time can access your data, your program is single threaded. Common lock-gotchas include locking writes but not reads!

#### The `final` keyword
Making variables `final` addresses all three issues, but requires your state to be immutable. This approach is most useful in composition with other techniques.

#### Use guava libraries
Google has published a library of thread-safe collections that handle concurrent access for you. ConcurrentHashMap is one of my favourites, and you can check out the [user guide](https://github.com/google/guava/wiki) for more.

<hr />

#### Note
> * A variable's declaration and assignment are really two operations: the allocation of memory to hold the value, and the writing of that value into memory. If a variable is declared and assigned within an object constructor, the object will not be published without variable initialization. However, if it is declared and assigned anywhere else, there are no such guarantees. For the purpose of this example, I am completely ignoring these implications :D
