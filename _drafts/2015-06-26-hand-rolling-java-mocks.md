---
layout: post
title:  Test Driven Development in Java - An alternative to mocking libraries
date:   2015/06/29
categories: java
---
Test-Driven Development in Java - Hand made mocks
---------------

Mocking in unit tests
=============
Mock objects are great when we are testing with objects that lie on the "boundaries" of an application. We can mock interactions with the following to create fast and manageable unit tests.

* Databases
* File Systems
* Web Sockets
* Message Queues
* And so on...


Mock objects are predominantly used to either create a stub that returns canned data, or to spy on the method invocations it has received from other objects.

In the past, I would use mocking libraries as the primary way of defining mock objects, and I felt really productive whilst using them. Mocking in this way is very quick and straight forward, and there are times when their usage is ideal. Therefore, the purpose of this article isn't to completely dissuade you from using them. The problem that I have observed is when developers become too dependent on mocking libraries (like I was), and can cause unnecessary pain especially when it comes to change. Instead, there are cases where simply writing your own hand made mocks can result in unit tests that are far more decoupled from your collaborators.

So, what is the problem with using mocking libraries?

Misplaced Behaviour
============
Lets pretend we are testing some class that is interacting with the file system. Our class is going to delete all files except for the newest file. We have decided to use iteration to undertake this task, which will look something like the following;

{% highlight java %}
while(fileSystem.numberOfFiles() > 1){
  fileSystem.deleteOldestFile();
}
{% endhighlight %}

As we are dealing with the file system, we want to mock this interaction. How would we mock this using a library like Mockito?

{% highlight java %}
FileSystem fileSystem = mock(FileSystem.class);
when(fileSystem.numberOfFiles()).thenReturn(2); //return 2 during the first call
when(fileSystem.numberOfFiles()).thenReturn(1); //return 1 on subsequent calls
verify(fileSystem).deleteOldestFile();          //check deleteOldestFile was called
{% endhighlight %}

The problem is that we have to assume how the `FileSystem` works underneath. We either have to guess how the `FileSystem` handles file deletions, or we have to peak into how `FileSystem` works. Either way, that behaviour does not really belong in the unit test we are currently writing. This test has become highly coupled to the implementation of the `FileSystem`. Any changes to the `FileSystem` may result in changes to this class.

Duplication
===========
Another problem with mocking libraries is duplication of mock behaviour. If we were to read a file from the `FileSystem`, we could stub out that interaction as follows;

{% highlight java %}
FileSystem fileSystem = mock(FileSystem.class);
when(fileSystem.readFile("testFile.txt")).thenReturn("TestFileContent");
{% endhighlight %}

Very straight forward. On the other hand you may imagine the `FileSystem` being used in many places to read files, and therefore this mocked behaviour may be duplicated in lots of different unit tests.

Once again, the unit test is tightly coupled to the `FileSystem`. If our `FileSystem` class were to change, then you will have to drill into multiple unit tests to make the change. This leads to unnecessary developer effort that could be better spent elsewhere.

Hand made mocks
-------------
The issues I have highlighted are really down to duplication/tight coupling between unit test and collaborator. To make our unit tests react to change more comfortably, we can create out own hand made mock objects instead. Mock objects are easily reusable and all of the mock behaviour is in one place.

Going back to our `FileSystem`, we could create our own mock implementation which could pretend to read files as follows;

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

This `MockFileSystem` can be reused across all unit tests needing access to the file system. If the behaviour of the file system were to change, for e.g. we now want to use the `java.io.File` class to identify files instead of a `String`, we have far fewer places to change compared to mock behaviour being defined across many unit tests.

All we are doing doing here is standard object oriented programming, and we can make the mock objects as expressive as we want.

Hand made Spies
============
Another function that mocking libraries provide is the ability to check whether or not a method on a mock object was called correctly.

Going back to our file system, lets add a new method to delete a file. We will want to make sure that this method was called correctly;

Let us extend on `FileSystem`, this time recording what files were deleted. We will also delete the file from our MockFileSystem to ensure it is kept consistent with the real file system.

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

In our unit tests, we can now use the `getDeletedFiles()` method to check that a particular file was in fact deleted.

Summary
====
Mocking libraries can be useful for times when duplication isn't going to be an issue and the mock behaviour is simple. However if you find that you are duplicating mock behaviour in many unit tests, or your mock objects are non-trivial, then hand made mock objects may be a better option. I've found them to be a better option for protecting your unit tests against change.
