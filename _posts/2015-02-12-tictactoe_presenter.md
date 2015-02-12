
---
layout: post
title:  "Tic Tac Toe: Using the Presenter Pattern"
date:   2015-02-12 09:20:57
categories: tictactoe design patterns
---
The Problem
=============
My first Tic Tac Toe Game used a command line interface to display the board. I had a CommandLineInterface class which had a means of translating game state to user friendly messages.

I then extended this Tic Tac Toe to use a GUI display as well, and again had methods that would translate game state to messages that can be displayed to the user. Rule 3 in [The Four Rules of Simple Design](http://c2.com/cgi/wiki?XpSimplicityRules) states no duplication. I knew I was going against this, but didn't want to put UI specific logic in my Game class, and didn't want the different interfaces to depend on each other.

My third and fourth attempt at Tic Tac Tac was to work as a web app with Rack/Rails respectively. More UI logic had to be added here, i.e. CSS styling to size the board cells correctly, whether or not the page should be refreshed based on if the computer player is going next, and so on. Even though the Rack/Rails projects were in different code bases and were sharing the same gem, it felt wrong to copy and paste this UI logic among projects.

The Solution
=========

I didn't want any UI logic in my Game class as that'll violate the Single Responsibility Principle, nor did I want the various interfaces to depend on each other. The solution I used for this was the [Presenter Pattern](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter). I created a GamePresenter which did the following;

- Determined whether a computer player was going next (which could be used to issue a page refresh)

- Determined the size of the cells that'll be displayed in the web based UIs

- Determined what status should be displayed to the user, based on the game state

The result is that all of this UI logic is defined only once. More importantly my web controllers are thin, devoid of too much business logic, which are accompanied with small unit tests.
