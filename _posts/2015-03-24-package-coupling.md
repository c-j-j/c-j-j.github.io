---
layout: post
title:  Packaging Principles - Coupling
date:   2015/03/24
categories:
---
Package Coupling
========
When we create software components in the form of Java Jars, C# DLLs or Ruby gems, the two most important things that we want to get out of it are;

1. The ability to reuse components.
2. The ability to change components.

Reusable components are crucial to any software development team that wants to remain efficient. I've worked on projects where we have been able to put together new software applications that are mostly composed on existing components. This saves a lot of work when done right.

There are three package coupling principles which I will go through in this blog post; The Acyclic Dependency Principle, the Stable Dependency Principle and the Stable Abstraction Principle.

The Acyclic Dependency Principle
-------------------
The Acycling Dependency Principle (ADP) says that if component A depends on component B, component B will not have any transitive dependencies on component A.
I didn't know that it was possible to violate this principle. With Java/Maven, you are (as far as I'm aware) not able to have any circular dependencies.

Say we had the following contrived dependency structure;

UserInterface -> Accounting -> StringUtils -> UserInterface

If we were testing the Account component which handles business logic/accounting rules, we'd need to depend on StringUtils.

Unfortunately, StringUtils is going to pull in the UserInterface dependencies too. So what is wrong with this?

1. Compile times are going to increase, you now have to compile UserInterface (and all of its dependencies) whenever you compile Accounting.

2. When you deploy the Accounting module, you will need to have the UserInterface component build for it to reference too.

3. You are not able to yank out the Accounting component to reuse on a different application without Accounting carrying around its extra baggage.

The Stable Dependency Principle
--------------------
When we are talking about components, stability is how easy it is to change a component. It does not have anything to do with how buggy the component may be. One metaphor that Uncle Bob uses is a coin balancing on a table. This can be said to be unstable, you are easily able to change its state. The table on the other hand is sturdy, and is harder to change the state. Therefore, you can say the table is more stable than the coin.

Going back to software, any component that has lots of components depending on it will be stable.

If we had components X, Y and Z depending on A;

(X, Y, Z) -> A

 A cannot change without cooperating with X, Y and Z, otherwise any change to A could break these components.

So how do we have reusable components that can react to change? The answer is to only depend on more stable components, and that is the crux of the Stable Dependency Principle.

Components which we expect to undergo many changes should have a minimum number of components depending on them. Components which we do not expect to change can have many components depending on them.

How we know which components should be more stable is what the next principle refers to.

The Stable Abstraction Principle
--------------------------
Lets say we had a class A that implements some interface. It should be obvious to say that we would expect more changes to occur in class A compared to the interface. Afterall, the interface is where high level abstractions reside and these abstractions tend to change less than their concrete implementations.

With software components, the Stable Abstraction Principle states that a component should be as stable as it is abstract.

Abstract interfaces change less frequently than their concrete implementations, so we want the concrete implementations to depend on the abstractions.

The advantage of this is that components that are highly depended upon are more abstract and therefore undergo less frequent changes. Components that are more concrete have less dependencies and therefore can change with more ease.
