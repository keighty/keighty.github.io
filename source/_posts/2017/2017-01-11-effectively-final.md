---
layout: post
title: "effectively final"
date: 2017-01-11
sharing: true
categories: [java]
---

I have been working on a Java concurrency bug at work for the last few weeks, and the intertwining concepts of immutability and publication have recurred several times. With the help of ["Java Concurrency in Practice," by Brian Goetz](https://www.amazon.com/Java-Concurrency-Practice-Brian-Goetz/dp/0321349601), I am starting to make sense of these concepts, and why these kinds of bugs can be difficult to reproduce.<!--more-->

### What does it mean to publish an object?

"Publishing an object means making it available to code outside of its current scope, such as by storing a reference to it where other code can find it, returning it from a nonprivate method, or passing it to a method in another class." (Goetz et al.)

An object that is published is available for use outside of its declared context. Publication can be direct or indirect. Storing a reference to an object in a public static field, directly publishes that object to any client of the class.

```java
public class PublishedObject {

    public static void main(String[] args) {
        System.out.println(DirectlyPublishedObject.stringArray[0]);

        DirectlyPublishedObject.stringArray[0] = "changed";

        System.out.println(DirectlyPublishedObject.stringArray[0]);
    }

    public static class DirectlyPublishedObject {
        public static String[] stringArray = {"gotcha"};
    }
}

/*
* Console output:
* gotcha
* changed
*/
```

`DirectlyPublishedObject.stringArray` can be retrieved and modified by any client that has access to the class (in this case, `PublishedObject#main` is the client). An object can also be indirectly published if it is a visible member of another object that is published:

```java
public class PublishedObject {

    public static void main(String[] args) {
        IndirectlyPublishedObject ipo = new IndirectlyPublishedObject();
        String[] myPersonalStringArray = ipo.getStringArray();

        System.out.println(myPersonalStringArray[0]);
        myPersonalStringArray[0] = "changed again";
        System.out.println(myPersonalStringArray[0]);
    }

    public static class IndirectlyPublishedObject {
        private String[] stringArray = {"gotcha"};

        public String[] getStringArray() {
            return stringArray;
        }
    }
}

/*
* Console output:
* gotcha
* changed again
*/
```

If ObjectA obtains a reference to ObjectB, it doesn't matter if ObjectB is declared private, it has now been publicly published.

Why does publication matter? Shared objects have different publication requirements depending on whether they are mutable. From Goetz:

* "Immutable objects can be published through any mechanism;
* Effectively immutable objects must be safely published;
* Mutable objects must be safely published, and must be either thread safe or guarded by a lock."

### What does it mean to for an object to be immutable?

An immutable object must satisfy these criteria:

* The class is not extensible (no methods can be overridden)
* All fields are marked final
* All fields are assigned in the constructor
* No internal state is modifiable by the client of the class (ie: no setters)
* No fields that reference mutable objects are made available to clients of the class

```java
public final class ImmutableObject {
    private final String name;

    public ImmutableObject(String name) {
        if (null == name)
            throw new IllegalArgumentException("Must supply a valid name.");

        this.name = name;
    }

    public String getName() {
        return name;
    }

    public static void main(String[] args) {
        ImmutableObject io = new ImmutableObject("keighty");
        System.out.println("name = " + io.getName());
    }
}
```

The ImmutableObject will not have any state other than a single field `name` that is initialized to the String value passed to its constructor. Because `name` is marked `final` and initialized in the constructor, there is no danger of encountering an instance of ImmutableObject in the wild whose name is not initialized.

If an object violates *some* of the requirements for immutability, but its state will never be modified after publication, it will still be treated as immutable by the compiler and JVM -- it is __effectively immutable__.

### The String class is "effectively immutable"

In Java, an instance of the String class is not technically immutable, even though Strings are constant and cannot be modified after creation. They have another property (a private hash) that is lazy loaded only as it is needed:

{% img /images/170111-effectively-final/string_constructor.png %}
{% img /images/170111-effectively-final/string_lazy_hash.png %}

A String is considered effectively immutable because its internal state is completely encapsulated and its external state isn't changed after it has been published. Even though the hash field is not final, and is only assigned as it is required (a performance optimization), it is not modifiable by any client. The program can treat Strings as if they are immutable, even if they don't strictly fit all of the criteria.

### How can I make my objects effectively immutable?

By ensuring that the internal state is completely encapsulated, and any mutable internal objects returned by a public method are copies, not references, you too can enjoy the benefits of at-will publication.

```java
public class ImmutableObject {

  public static class EffectivelyImmutableObject {
    private String[] stringArray = {"gotcha"};

    public String[] getStringArray() {
      // return a COPY of the mutable object, not a reference
      return Arrays.copyOf(stringArray, stringArray.length);
    }
  }

  public static void main(String[] args) {
      EffectivelyImmutableObject eio = new EffectivelyImmutableObject();
      String[] myPersonalStringArray = eio.getStringArray();

      System.out.println(eio.getStringArray()[0]);

      myPersonalStringArray[0] = "changed a copy only";

      System.out.println(myPersonalStringArray[0]);
      System.out.println(eio.getStringArray()[0]);
  }
}
/*
* Console output:
* gotcha
* changed a copy only
* gotcha
*/
```

#### RESOURCES

* Peierls, Tim; Goetz, Brian; Bloch, Joshua; Bowbeer, Joseph; Lea, Doug; Holmes, David (2006-05-09). Java Concurrency in Practice. Pearson Education. Kindle Edition.

* [javadoc for String](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html)
