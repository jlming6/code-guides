# Code Guide

Mainly include Java code principles, tips, traps, pitfalls and corner cases.

*This is not a Java basic syntax guide or a Java introduction.*

## References:

* [[Effective Java]](http://www.amazon.com/Effective-Java-Edition-Joshua-Bloch/dp/0321356683)
* [[Clean Code]](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
* [[JPL Java Coding Standard]](http://lars-lab.jpl.nasa.gov/JPL_Coding_Standard_Java.pdf)
* [[Java Concurrency in Practice]](http://www.amazon.com/Java-Concurrency-Practice-Brian-Goetz/dp/0321349601)
* [[Java Puzzlers]](http://www.amazon.com/Java-Puzzlers-Traps-Pitfalls-Corner/dp/032133678X)

## Table of Contents

* [Principle](#principle)
* [Class](#class)
* [Object Create & Destroy](#object-create--destroy)
* [Function](#function)
* [Unit Test](#unit-test)
* [Exceptions](#exceptions)
* [String](#string)
* [Concurrency](#concurrency)
* [Reflection](#reflection)
* [MISC](#misc)

## Principle

* Apply the static analysis after finishing coding.

* Errors should be detected as soon as possible after they are made, ideally at compile time.

* Reduce the __*magic things*__ in code. Do NOT depends on magic things.
    * What is magic thing?
    * Out of Control: GC, Thread Synchronization, etc.
    * Hard to understand: Clone, etc.

* Reduce the possibility of misunderstanding by Compiler, JVM and other programmers.

* Make class as immutable as possible.

* Defensive Coding.

* Premature optimization is the root of all evil.

* Consistent abstract level.

## Class

* [SRP](http://en.wikipedia.org/wiki/Single_responsibility_principle)

* Always use @Override.

* Minimize the accessibility of classes and members.
    * Information hiding
    * Encapsulation

* Favor composition over inheritance.

* Prefer interface to abstract class.

* Never depends on Java instance initializer order.

* Never redefine fields in a subclass.
    * This will hide the field in superclass, and this is magic.

* Avoid cyclic class dependencies.
    * It is difficult to understand.
    * Sometimes, this means the responsibility is not clear.

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

* The clone() method should never be overridden or even called.

* Make singleton or helper class constructor private.

* Pay attention to memory leak in:
    * Container
    * Cache
    * Listener and other callback.

* Avoid finalizer because it is unpredictable, often dangerous, and generally unnecessary(Magic).
    * Use try - finally instead.
    * Finalizer will hide exceptions.
    * May cause decreased performance.

## Function

* Do one thing, and do it well.

* No side-effect (magic).
    * Do NOT do thing that can not be known from the function name.

* Pay attend to function that will change the state of object.

* **equals** rule:
    * [Equivalence relation](http://en.wikipedia.org/wiki/Equivalence_relation):
        * Reflexive
        * Symmetric
        * Transitive
        * Consistent
        * Non-nullity(null sensitive)
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

* Return empty arrays or collections, not nulls.

## Unit Test

* Unit test cases should be independent from any externel resources, like DB, HTTP service, etc.
    * for portal.
    * for performance.
    * for stable.

* Unit test cases should be stable and repeatable.

* Unit test cases should be fast.

* Unit test cases should be independent from each other.

## Exceptions

* Good exception:
    * A clear message indicating that an error occurred.
        * Meaningful message: help to recover the exception content
    * An ordered classification.
        * Fatal
        * Error
        * Warning


* Use exceptions only for exceptional conditions.
    * What is exceptional condition?

* Use unchecked exceptions for most conditions exception you have good reasons to use checked exceptions.

* **Throw exceptions appropriate to the abstraction**.
    * That is one reason why it will be better to use unchecked exception.
    * Check exception will expose the implementation.

* Include failure-capture context information in detail messages.

* Stive for failure atomicity(Transation).

* Never ignore(swallow) exceptions.

* Do not abruptly exit a finally block.
    * A finally block should not contain a return statement and should not
    throw any exceptions that are not caught within the finally block.
    * A finally block is entered when the try block finishes, reguardless of
    whether it finishes normally or abnormally by throwing an exception.
    The case of concern is where the try code throws an exception giving rise
    to two distinct situations where the exception is lost. First, if the
    finally block contains a return statement, then the original exception will
    be discarded. Second, if there is an uncaught exception thrown in the
    finally block, then the original exception will be discarded.

## String

* Avoid string where other types are more appropriate.
    * Enum
    * Aggregate object. it is usually a bad idea to **_represent_** the object as a single string.

* Beware the performance of string concatenation.
    * Use StringBuilder to concatenate strings.

## Concurrency

* Synchronization has no effect unless both read and write operations are synchronized.

* Should do as little work as possible inside synchronized regions.

* Prefer executors and tasks to threads.

* To enable safe concurrent use, a class should clearly document what level of thread
safety it supports.
    * immutable
    * unconditionally thread-safe
    * conditionally thread-safe
    * not thread-safe
    * thread-hostile

* Never depend on the thread scheduler(magic).

* Thread Group?

## Reflection

* Prefer interface to reflection.
    * Lose all the benefits of compile-time type checking.
    * The code required to perform reflective access is clumsy and verbose.
    * Performance suffers.


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

* Avoid float and double if exact answers are required.

* Prefer primitive types to boxed primitives.
    * When you mix primitives and boxed primitives in a single operation, the boxed primitive is auto-unboxed.
    So if a null object reference is auto-unboxed, you get a NullPointException

* For interval timing, always use System.nanoTime in preference to System.currentTimeMillis.
    System.nanoTime is both more accurate and more precise, and it is not affected by adjustments to the system's real-time clock.

* Implement Serializable judiciously
    * A major cost of implementing Serializable is that is decreases the flexibility
    to change a class's implementation once it has been released(exported API).
    * A second cost of implementing Serializable is that it increases the likelihood
    of bugs and security holes.
    * A third cost of implementing Serializable is that is increases the testing
    burden associated with releasing a new version of a class.

* Avoid to use wildcard(*) in import.

* Use breaks in switch statements.
    * Bad case: Murmur Hash Function in common-utils.

* Restrict side effects in expressions.
    * The assignment operator, +=, ++, etc.
    * Any other write operator.
    * Too magic.
