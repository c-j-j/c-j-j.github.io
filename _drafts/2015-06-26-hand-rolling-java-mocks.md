---
layout: post
title:  Hand made mocks in Java
date:   2015/06/29
categories: java
---
Hand made mocks in Java
---------------

Why not use JMock/Mockito?
===========
There was a time not too long ago where I would vehemently argue that you should use a mocking library when doing Test Driven Development in Java. I used to mock everything with JMock, and I felt really productive whilst using it. With these libraries, you can quickly define the behaviour of the collaborators of a particular class, without having to create any new objects. The purpose of the blog isn't to completely deter you from using this libraries, they are very useful at times. Instead I want to present to you why you shouldn't be too dependent on these libraries, and what alternatives there are.

Duplication
===========
By using Mockito, lets stub out a simple string validator that'll always return true. We can pass this to to an object under test.

{% highlight java %}
Validator<String> validator = mock(Validator.class);
when(validator.isValid(anyString())).thenReturn(true);
{% endhighlight %}

Very straight forward. However, if this validator class was then used by several other classes, then this setup will be duplicated in every test suite. What is the problem with this?

Imagine a new requirement has presented itself such that it requires us to change our validator class to now return some <code>Result</code> object instead of a boolean. This small change would cause compliation issues in many places, and suddenly you have to drill into multiple places in order to fix this. More change means more risk that something could break, and unnecessary change is something that we should aim to minimize. (With JMock you won't even get compilation errors, instead your tests will simply fail due to the invalid types that your stubs return.)

Unchecked type casts in tests
===============
In the previous code, we were stubbing a <code>Validator</code> class that makes use of generic types. Unfortunately, due to type erasure, we are not able to explicately state that we wish to mock a <code>Validator</code> class with a concrete type of <code>String</code>. We mocked a raw Validator class and assigned it to a Validator of type String. If you were to type the code above into an IDE like IntelliJ, it will give you a warning that there is an unchecked assignment; <code>Unchecked assignment: 'Validator' to 'Validator'`<java.lang.String`> </code>

To fix this, you would have to clutter up your test suite with suppression statements to make these warnings go away.

Hand made stubs
===================


Points to make

* Vocabulary
Jmock/Mockito have
  * syntax
  * difficult to reuse
  * Loss of parameterized type
  * Having to cast
  * Unable to mock/stub generic types
  * restrict change due to type checking

Advantages of hand rolling
  * No new syntax to learn, API to reference
  * Reusable
  * Able to define complex stubs easier
  * Able to write stubs/spies with type
