---
layout: post
title:  Test Driven Development in Java - An alternative to mocking libraries
date:   2015/06/29
categories: java
---
Test Driven Development in Java - An alternative to mocking libraries
---------------

Mocking in unit tests
=============
Mock objects are great when we are testing objects that lie on the "boundaries" of an application. For example, if we are interacting with databases/file systems/web sockets/message queues and so on, we can mock these interactions to result in fast/manageable unit tests.

It is imperative to check that class under test is interacting with its collaborators correctly. I used to make heavy use of the JMock mocking library to define mock objects, and I felt really productive whilst doing it. Mocking with these libraries is very quick and straight forward, and they allow you to make assertions that methods on the mock objects are being called correctly, etc. Because they are quick and easy to get setup there are times when their usage is ideal, and the purpose of this article isn't to completely dissuade you from using then. The problem I have observed is developers becoming far too dependent on mocking libraries, where even things like factories and value objects are mocked. Overuse of these libraries can cause pain which I have experienced firsthand. There are alternatives to always using a mocking library that I believe work better in many cases. After reading this article, I hope that in future you will question the need for a mocking library the next time you are thinking about using one.

So, what is the problem with them?

Intimate knowledge of collaborators
=============
One of the main goals of object oriented programming is to have decoupled objects. These objects typically contain private data, and are knowledgeable on how handle that data.

Lets say we are writing a TicTacToe application, and we are writing a test for the `Game` class which uses a `Board` class. If we were to use a mocking library to define a `MockBoard` object inside of `GameTest`, then we have to know how `Board` works. It'll be tricky to create a `MockBoard` within `GameTest` without having a peek behind the curtain to gain an understanding for how our real `Board` works, especially as our Board object will contain state like the previously made moves. The reason this makes me feel uneasy is that if `Board` were to change, the mock behaviour specified in our `GameTest` is likely to also change.

Let's pretend that you and I were working on the same project, and you have mocked the behaviour of a class that I had worked on 3 months ago. I, minding my own business, make a change to this particular class and BOOM, several unit tests which you wrote are now failing because the mock behaviour defined in those tests is now out of date. Because I'm a nice guy, I take it among myself to fix those unit tests which I broke. It's going to require me to study code which I've not worked on before in order to fix them. Areas of potential change is always something to be aware of when writing code, and from experience mocking libraries can make change more painful than it could be.

The Duplication
===========
By using Mockito, lets stub out a simple string validator that'll always return true. We can pass this to to a class under test.

{% highlight java %}
Validator<String> validator = mock(Validator.class);
when(validator.isValid(anyString())).thenReturn(true);
{% endhighlight %}

Very straight forward. However, if this validator class was then used by several other classes, then this setup will be duplicated in every test suite. What is the problem with this?

Imagine a new requirement has presented itself such that it requires us to change our validator class to now return some <code>Result</code> object instead of a boolean. This small change would cause compliation issues in many places, and suddenly you have to drill into multiple places in order to fix this. More change means more risk that something could break, and unnecessary change is something that we should aim to minimize. (With JMock you won't even get compilation errors, instead your tests will simply fail due to the invalid types that your stubs return.)

Object incompability
================
When we make heavy use of mocking (with or without a mocking library), it can lead to unit tests that aren't giving our objects the chance to really work together like they would in production. What I have seen in the past is that despite an entire suite of unit tests passing, high level acceptance tests (which are not using any mock objects) are failing due to imcompatability issues between the real objects. This then forces us to refine the behaviour of our mock objects to make them more realistic. I personally feel that its best to use real objects in unit tests when it is feasible to do so, as the unit tests are more substantial/valuable.

The alternatives
-------------

Hand made Mocks/Stubs/Spies
===================
Some of the issues I have highlighted above really due to knowledge defined in unit tests where it doesn't belong. When we define mock behaviour in a unit test for a collaborator, then those unit tests must change whenever the collaborator changes.

What I usually prefer to do is to have my own mock objects which I control and can reuse. If we were to stub out the file system, we could put together a simple interface as follows;

{% highlight java %}

public interface FileSystem{
  String readFile(String filename):
}

{% endhighlight %}

We can create a simple stub system like the following;

{% highlight java %}
public static class MockFileSystem implements FileSystem {

  private Map<String, String> files;

  private MockFileSystem(Map<String, String> files){
    this.files = files;
  }

  public static MockFileSystem createWithFiles(Map<String, String> files){
    return new MockFileSystem(files);
  }

  @Override
  public String readFile(String filename){
    return files.get(filename);
  }
}
{% endhighlight %}

This stub file system can be reused across all tests needing access to the file system. If the behaviour of the file system were to change, for e.g. we now want to use the `java.io.File` class to identifier files instead of a `String`, we have far fewer places to change compared to mock behaviour being defined across many unit tests.

Also observe how the method name `createWithFiles` is descriptive. All we are doing doing here is standard OO programming, and we can make the mock objects as expressive as we want.

Hand made Spies
============
Another function that mock objects provide is the ability to check whether or not a method on that mock object was called correctly.

Going back to our file system, lets add a new method to delete a file. We will want to make sure that this method was called correctly;

{% highlight java %}

interface FileSystem{
  String readFile(String filename):
  void deleteFile(String filename);
}

{% endhighlight %}

Let us extend on FileSystem, this time recording what files were deleted. We will also delete the file from our MockFileSystem to ensure it is kept consistent with the real file system.
{% highlight java %}

public static class MockFileSystem implements FileSystem {

  private Map<String, String> files;
  private List<String> deletedFiles;

  private MockFileSystem(Map<String, String> files){
    this.files = files;
    deletedFiles = new ArrayList<>();
  }

  public static MockFileSystem createWithFiles(Map<String, String> files){
    return new MockFileSystem(files);
  }

  @Override
  public String readFile(String filename){
    return files.get(filename);
  }

  @Override
  public void deleteFile(String filename) {
      files.remove(filename);
      deletedFiles.add(filename);
  }

  public List<String> getDeletedFiles(){
    return deletedFiles;
  }
}
}
{% endhighlight %}


Real objects
===========
Mock objects are great when we are testing objects that lie on the "boundaries" of an application. For example, if we are interacting with the database/file-system/web sockets/message queues and so on, then we are far better off mocking the interactions during these types of things.

However, when we are not at these boundaries, and it can be easier to reuse the objects that are being created if the
