---
layout: post
title: Checked Exceptions
date: 2015/06/09
categories: checked exceptions
---
Checked Exceptions
====

For the past week, I have been writing a HTTP Web Server in Java. This is the first time I've really used Java since joining 8th Light and honestly, it hasn't been a completely happy reunion. There's lot of things with Java I do still like.

However, there's one thing that really bothered me this week, and that was the fact that checked exceptions are still very prevalent in the language. For example, any interaction with any IO, e.g. reading/writing files, working with streams and readers, all seem to throw checked IOExceptions.

When I want to read from a file and an IOException occurs, I have three choices

- Re-throw the checked exception. This means that any dependencies now have to handle/re-throw the checked exception.
- Handle the checked exception there and then, however this means multiple places are doing exception handling. This could result in duplication and SRP violations.
- Wrap the checked exception in an unchecked exception, and ensure you handle that exception elsewhere. This is the option I usually go for as I'm now back in control of how to handle exceptions

My main issues with checked exceptions, and in particular the checked IOException in Java, are as follows;

1. Exception handling everywhere in your code.

With my HTTP Server, I composed an architecture such than whenever a runtime exception would occurred, that exception would bubble up the stack and will eventually be caught. Once caught, the server would return a 500 status code and log the error. One and only one place was doing that exception handling, and only one place needs to change if I want to change the exception handling.

![Exception Handling](/assets/ExceptionHandling.png)

Even though the exceptions were channelled to a single place, I still have this to do something like this whenever I work with IO;

{% highlight java %}
try{
  FileUtils.read(someFile);
}catch(IOException e){
  throw new UncheckedIOException(e);
}
{% endhighlight %}

This makes the code look somewhat clumsy and untidy.

2. Violating Open/Closed principle

When you declare that a public method should throw a checked exception, that is very likely going to break everywhere that uses that method. The same occurs when you want to remove a checked exception from being thrown. Code should be closed for modification, yet when checked exceptions are introduced, suddenly you are forced to modify code that should not have any reason to change.

3. Violating Single Responsibility Principle

Classes/methods should have one reason to change. When you have your exception handling logic mixed in with your file reading logic, you now have two reasons to change. Furthermore, file reading could be considered fairly low level, whereas you may have some high level exception handling strategy (e.g. log the error) that you want to happen when an exception occurs. If you were to handle the error where it was thrown, your file reading classes now need a dependency to some logging functionality.

3. Difficulty in testing certain exceptions

The file system can be a problematic domain, especially with file permission issues, disc space issues, and so on. However simulating these file issues in a unit test isn't trivial. What's worse, different OS's work with files differently, and an operation that fails on a Windows machine may work on a Linux machine. Furthermore, libraries such as Apache commons seems to try and minimize exceptions by occurring by creating files that do not exist, and so on. To properly test things like IOExceptions, we have to do a fair amount of setup in order to get the exception to be thrown.

Summary
----------
Your application must have a well designed exception handling process baked into its core achitecture. When you suppress checked exceptions and start only having runtime exceptions flowing in your system, the more control you have on your application.
