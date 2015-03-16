---
layout: post
title:  "To Mock Or To Stub"
date:   2015-03-13 09:20:57
categories: mock stub
---
When it comes to unit testing an object that has collaborators, it is necessary to use either a mock or a stub.

What's the difference? A stub is an object that returns some data. It does not have any assertion logic built into it. A mock on the other hand contains assertion logic, and is usually automatically evaluated at the end of a unit test. We will also mention spys, which are objects that record method invocations but doesn't contain any assertion logic.

The simple rule of thumb that I follow is this;

1. When your object is requesting data from a collaborator, stub that response. You needn't check that it was called in your unit test. Presumably, you are calling this object in order to change some state in the object you are testing. That change of state is what you should be testing.

2. When your object is invoking a command against a collaborator, mock that object and assert that the method invocation was made with the correct arguments.

In other words, use stubs for inbound messages to the object under test, and mocks for outbound.

What if we have a collaborator where we have both outbound and inbound messages going to and from the object under test. This sounds a little complex, and could indicate a code smell (maybe Tell, Don't Ask was violated). Nonetheless, this situation can arise. There are three options you have;

1. Mock the behaviour in your unit test using a mocking library like JMock. The issue with this is you'll tightly couple your test to the implementation of the complex collaborator. The slightest change to the collaborator will break this unit test. You'll be in a world of pain if you've mocked this collaborator in multiple tests in this way.

2. Create a hand made stub/spy that mirrors your collaborator in your unit tests, make the assertions in your unit test to check that the object was called correctly. This hand made mock can be reused across multiple unit tests. If the collaborator were to change, only the hand made objec needs to change. 

3. Use the real object in your test. I have used this approach with my TicTacToe, where I used a real <code>Game</code> object when testing my <Code>UserInterface</Code> class. The advantage with this is there's no mock object tightly coupled to your collaborator. You can make assertions against the object under test, and the collaborator that it was interacted with correctly.

Ultimately, what we are striving for are unit tests that are not tightly coupled to the implemenation. On the other hand, coupling the tests to the interfaces of the collaborators is, in my opinion, okay. We have to test those interactions, yet because interfaces tend to change less frequently compared to the implementation details of those collaborators, our tests should be easier to change at a later date.
