# Code Style

This Java code style recommends best-practices so that programmer can write code that is easy-understanding and maintainable.

**Be Consistent**

Reference: [Google Java Code Style](http://google-styleguide.googlecode.com/svn/trunk/javaguide.html)

## Table of Contents

* [Source Code Layout](#source-code-layout)
* [Naming](#naming)
* [Comments](#comments)

## Source Code Layout

* Use `UTF-8` as the source file encoding.
    * If you're using Maven, just add following in your root parent pom.xml:

    ```xml
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    ```

* Use Unix-style line endings.
    * If you're using Git and work on Windows, add git config:

      ```
      git config --global core.autocrlf true
    ```

* Format
  * You can find all those settings in both Eclipse and IntelliJ.
  * Use four spaces per indentation level. No hard tabs.
  * Add space around binary-operator, after `for`, `while`, etc.
  * Braces are used with if, else, for, do and while statements, even when the body is empty or contains only a single statement.
  * One statement per line.

* File Structure
  * No wildcard imports

## Naming

* Use meaningful names.
* Use searchable names.
  * Easy to search, easy to refactor.
  * Use `CurrentVideoType` instead of `Type`
* Avoid encoding.
  * `showNameString` is not better than `showName`
* No member prefixes.
  * Not recommend any prefix:

    ```Java
    public class Video {
      private String _name;
      private String m_title;
      private String description_;
    }
    ```

* Classes and objects should have noun names.
* Method should have verb names.
* Avoid using abbreviations. For example, use `firstName` instead of `fName`.
* Avoid generic names like `number`, `process`, `manager`.
* Use the prefixes `get` and `set` for *getter* and *setter*, Use `is` and `has` for methods returns bealean value.
* Conventions:
  * Packages use Lowercase

    ```Java
    package com.hulu.recommendation
    ```

  * Variables use Mixedcase

    ```Java
    string videoTitle;
    int packageGroupId;
    ```

  * Classes and Interfaces use CamelCase

    ```Java
    class Video
    interface Filter
    ```

  * Methods use Mixedcase

    ```Java
    void filterResult
    string getVideoTitle
    bool isVideoValid
    ```

  * Constants use Uppercase

    ```Java
    static final int DEFAULT_PACKAGE_GROUP_ID
    static final int MAX_HEIGHT
    ```

## Comments

> Don't comment bad code--rewrite it.
>
> -- Brian W.Kernighan and P.J.Plaugher

* Comment for What?
  * Why you did this, why you did the decision.
  * Warning.
  * Todo things.
* Do NOT comment for What?
  * Obviously

    ```C#
    // parse user_id
      UserId = ParamParser.ParseInt32(Params["user_id"], -1);
      if (UserId < 0)
      {
          UserId = ParamParser.ParseInt32(Params["uid"], -1);  
      }
    ```

  * Change log, can keep in Git.
  * Commented-Out code.
* Keep comments update, can keep in Git.
