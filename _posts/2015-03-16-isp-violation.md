---
layout: post
title:  "How I violated the Interface Segregation Principle"
date:   2015-03-16 09:20:57
categories: interface segregation principle
---
Over the past few days I've been studying the SOLID principles, by watching the Clean Code videos as well as reading Principles, Patterns And Practices by Uncle Bob.

Although I've known about the SOLID principles for quite some time, I never realised quite how far and wide the principles reach when you are writing code.

For my first attempt at TicTacToe in Ruby, my code underwent many changes as new features were added. In hindsight, I did a poor job in keeping the abstractions correct as more features were added, and I ended up violating violating some of the SOLID principles in certain areas.

In this post, I'll describe how I violated the Interface Segregation Principle (ISP).

ISP tells us that no classes should be forced to depend on methods it does not use.

When you override a method in an interface to only return a null response, or throw a <code>NotToBeUsedException</code> of some sort, you could be violating ISP.

Initially, my TicTacToe used a CommandLineInterface, so I had an interface that looked like this;

<code>
public interface:
  int getUserMove();
  void printError();
  void printOutcome();
</code>

This worked fine for my CommandLineInterface, as when it was the human players turn, we requested that they enter their move.

The next feature came along to add a GUI client. I reused this interface, but did the following;

<code>
int getUserMove()
  return MOVE_NOT_AVAILABLE
</code>

This is undoubtedly a hack. With a GUI client, moves are not requested by the user, but instead the user submits a move when they are ready. The user drives the game forward and we used an event driven architecture to achieve this by using another function.

The point is that CommandLineInterface and GUIInterface do not share the same behaviour all of the same behaviour. The GUI client is event driven with asynchronous behaviour, whereas the Command Line is a synchronous client which blocks the control flow when the user move is requested.

So, how would we fix this? One way could be that we need two interfaces;

<code>
  public interface synchronousInterface
    int getUserMove()

  public interface UserInterface
    void printError();
    void printOutcome();
</code>

Now we have segregated the interfaces, the GUI interface can implement methods only included in the <code>UserInterface</code>.

ISP goes further than only overriding the required methods in an interface. Whenever you have some code that is forced to do something it doesn't need to do, you could be violating ISP. This could be having to pass in parameters to a method that the method doesn't require, or passing an unused parameter in a constructor.

Hacks are easy things to do, especially when you're in the zone and are just trying to get something working. The problem is they add up, and will add unnecessary confusion.
