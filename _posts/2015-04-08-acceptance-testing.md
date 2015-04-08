---
layout: post
title:  Acceptance Testing with a DSL
date:   2015/04/08
categories: acceptance testing
---
Acceptance Testing with a DSL
----------

Whilst reading [Clean Coder](http://www.amazon.co.uk/The-Clean-Coder-Professional-Programmers/dp/0137081073) by Uncle Bob, there is one chapter dedicated to acceptance testing.

In this chapter, he stresses on the importance of using a testing library such that acceptance scenarios can be written in plain English, and he recommends using a tool like Cucumber, Fitnesse, JBehave, etc.

However, I disagree this this additional level of abstraction is always strictly necessary when it comes to acceptance testing.

The argument for using acceptance tests written in plain English is that non technical employees can read and write them with a little amount of training.

One has to question just how often non-technical people are actually writing these scenarios without developer assistance, and is it really their remit to even do so.

Now there may be scenarios where plain English tests are preferable. For example, it could work for a team working on a mature application where there is a small amount of change to the core application, and instead most of the work is maintenance/additive. For these types of applications, most of the new features follow a particular pattern, and therefore plain English testing may be fairly easy to achieve.

However, for applications not in their maturity, or for more complex applications where new features differentiate from each other, then I feel a well written DSL would work better. 

We should always strive to write beautiful code, whereby we compose functions/variables that clearly explain the intent of the software. We can (and should) make code readable to a non-technical person, and have high level policy separated from the low level details. A DSL can be readable to a non-technical person, yet have the advantage of flexibility and to be able to test complex scenarios easier than the plain English counterpart.

To summarise, I don't see a huge amount of difference between a well written DSL and a plain English testing library in terms of readability. Given that the majority of the time these tests will be written by developers themselves, the DSL has the advantage of being more flexible with less work.
