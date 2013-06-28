# Code Guide

Mainly include Java code principles, traps, pitfalls and corner cases.

*This not a Java basic syntax guide or a Java introduction.*

## References:

[[Effective Java]](http://book.douban.com/subject/1246129/)

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

## Principle

* Errors should be detected as soon as possible after they are made, ideally at compile time.
 
* Reduce the magic things in code.

## Class

## Object Create & Destroy

* [**Recommend**] lightweight constructor.
	* What - Lightweight:
		* Limited parameters.
		* Only field assignment.
		* Not create other objects, delay to required for basic data structure, inject the other heavy objects; same as field declaration.
		* Not call virtual methods. Override will be called before the object has been fully instantiated.
	* Why:
		* Testable.
		* Single Responsibility.
			* This object are not responsible for how to create dependencies, just know how to use(API).
		* No magic things.
	* How:
		* Inject heavy objects.
		* User static factory or builder pattern.
		 
* [**Recommend**] throw exceptions from constructor, if appropriate.

* [**Recommend**] static factory methods instead of constructors.
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
	* Instance control for immutable objects.
	* Static factory methods can return objects of subType, this will decouple the interface implementation and usage.
	* Reduce the verbosity of creating parameterized type instance.

## Function

## Operation

## Exceptions

## String

## Concurrency

## Reflection
