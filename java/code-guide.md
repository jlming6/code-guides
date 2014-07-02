# Code Guide

Mainly include Java code principles, tips, traps, pitfalls and corner cases.

*This not a Java basic syntax guide or a Java introduction.*

## References:

* [[Effective Java]](http://book.douban.com/subject/1246129/)
* [[Clean Code]](http://book.douban.com/subject/3032825/)
* [[JPL Java Coding Standard]](http://lars-lab.jpl.nasa.gov/JPL_Coding_Standard_Java.pdf)

## Table of Contents

* [Principle](#principle)
* [Class](#class)
* [Object Create & Destroy](#object-create--destroy)
* [Function](#function)
* [Operation](#operation)
* [Exceptions](#exceptions)
* [String](#string)
* [Concurrency](#concurrency)
* [Reflection](#reflection)
* [MISC](#misc)

## Principle

* Errors should be detected as soon as possible after they are made, ideally at compile time.

* Reduce the magic things in code. Do NOT depends on magic things.

* Reduce the possibility of misunderstanding by Compiler, JVM and other programmers.

* Make class as immutable as possible.

* Defensive Coding.

## Class

* Always use @Override.

* Minimize the accessibility of classes and members.
    * Information hiding
    * Encapsulation

* Favor composition over inheritance.

* Prefer interface to abstract class.

## Object Create & Destroy

* **Recommend** lightweight constructor.
    * What - Lightweight:
        * Limited parameters.
        * Only field assignment.
        * Not create other objects, delay to required for basic data structure, inject the other heavy objects; same as field declaration.
        * Not call virtual methods. Override will be called before the object has been fully instantiated.(NOT safe)
    * Why:
        * Testable.
        * Single Responsibility.
            * This object is not responsible for how to create dependencies, just know how to use interface.
        * No magic things.
    * How:
        * Inject heavy objects.
        * User static factory or builder pattern.


* **Recommend** throw exceptions from constructor, if appropriate.

* **Recommend** static factory methods instead of constructors.
    * Static factory methods have its own name, better express the construct.
    * Can do complex things that are not inappropriate to do in constructor, like heavy initialization.

        instead of:

        ```Java
        class Item {
            public Item() {
                init();
            }

            protected void init() {
            }
        }
        ```

        recommend to use:

        ```Java
        class Item {
            protected Item() {
            }

            protected void init() {
            }

            public static Item newInstance() {
                Item item = new Item();
                item.init();
                return  item;
            }
        }
        ```

    * Instance control for immutable objects. That is factory is not required to create new object each time they're invoked.
    * Static factory methods can return objects of subType, this will decouple the interface implementation and usage. (Essentially, constructor is implementation instead of inferface/abstraction)
    * Reduce the verbosity of creating parameterized type instance.
      ```
      public static <K, V> HashMap<K, V> newInstance() {
          return new HashMap<K, V>();
      }
      ```
* **Recommend** builder when faced with many constructor parameters.
    ```
    NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);
    ```
    Hard to understand, right? Use Builder Pattern.

* Make singleton or helper class constructor private.

* Pay attention to memory leak in:
    * Container
    * Cache
    * Listener and other callback.

* Avoid finalizer because it is unpredictable, often dangerous, and generally unnecessary(Magic).
    * Use try - finally instead.

## Function

* **equals** rule:
    * [Equivalence relation](http://en.wikipedia.org/wiki/Equivalence_relation):
        * Reflexive
        * Symmetric
        * Transitive
        * Consistent
        * Non-nullity
    * Standard Example:
        ```
        @Override
        public boolean equals(Object obj) {
            if (this == obj) {
                return true;
            }

            if (!(obj instanceof Item)) {
                return false;
            }

            Item item = (Item) obj;

            if (item.getItemType() == getItemType() && item.getIdentifier() == getIdentifier()) {
                return true;
            }

            return false;
        }
        ```
    * Tips:
        * == only compare the reference, so please don't use it to compare objects.
            * Integer: [IntegerCache](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/6-b14/java/lang/Integer.java#Integer.valueOf%28int%29), [Problem](http://stackoverflow.com/questions/5277881/why-arent-integers-cached-in-java)
            * String: [Intern](http://en.wikipedia.org/wiki/String_interning)
            * Boolean
            * Float/Double, please use NumberUtils help function.
            * Enum (ok, [Reference](http://stackoverflow.com/questions/1750435/comparing-java-enum-members-or-equals#answer-2937561))
            * Other Objects
        * Always override hashCode when override equals.

* Validate function parameters.
    * Garbage In, Garbage Out.

* Make defensive copies when needed.

* Avoid long parameter lists.
    * <= 4
    * Hard to remember the order, and hard to understand.

* For parameter(input), more specific is better, for return(output), more general is better.

* Use overloading as few as possible.
    * A safe, conservative policy is never to export two overloadings with the same number of parameters.

## Operation

## Exceptions

## String

## Concurrency

## Reflection

## MISC

* Use Enum instead of int constant and boolean
    * Enums provide compile-time type safety.
    * Enum types with identically named constants.
    * Enum type could associate with data via declare instance fields and constructor.

* Prefer annotation to naming pattern
    * Examples:
        * JUnit: testMethodName
        * Serialization: fieldName
    * Reasons:
        * Typographical errors may result in silent failures. (no compile check)
        * Naming pattern can match one usage, but annotations could be added many ones.
