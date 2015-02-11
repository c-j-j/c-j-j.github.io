---
layout: post
title:  "Web Based Tic Tac Toe: Stateful vs Stateless"
date:   2015-02-11 09:20:57
categories: web tictactoe
---
The Requirement
=============

Over the past couple of weeks, I was assigned the task of extending my Tic Tac Toe game such that it can be played in a web browser. This required a fairly significant refactor of my core game logic in order to facilitate this change. Moreover, I had to extract the game loop out of my Game object and instead have the browser (or Command Line or GUI) drive the game forward.

There was one particularly tricky aspect of this task, the solution had to be **as simple as possible**. Usually when I'm building web apps, I'm using a framework like Django, front end JavaScript libraries such as JQuery/AngularJS, and a plethora of other tools to achieve what I want. Just by using Rack and not using JavaScript has been a fun and educational experience.

Note that my actual game of Tic Tac Toe supported the ability to choose a type of game (human vs computer, etc), and a board size. However for simplicity, I've not included that here and we are assuming we are working with a single game type and board size.

Approach One: Stateful backend
=========

My first approach was to have all of the state at the backend. To set the scene, here is how my Tic Tac Toe works.

![Tic Tac Toe Diagram](/assets/tictactoe.png)

To have a stateful backend, the Game was only created once at the start of the game. Once the game was created, this could then be stored in the session.

For subsequent moves, the following could then be carried out;

1. Retrieve game from the session

2. Check if move is valid

3. Register the new move with the game

4. Store the game back in the session

When a user made a move, that was placed in a query parameter that my Controller could handle.

Now I'm aware that storing object references in the session is perhaps not the greatest idea. In Rails, you aren't even able to store object references in sessions. Why did I do this? Because it's the simplest thing I could come up with that'll work.

The disadvantage with having the game stored in the session like this is that the user will not be able to play multiple games of TicTacToe at any one time. 

Approach Two: Stateless Backend
===========

The second approach was to adopt a more stateless backend, and having the state required for the gameplay transmitted via Query Parameters.

Along with the user's move transmitted via a query parameter, the current board is also stored as a query parameter, and is passed in with every request. The Controller now has more work to do, rather than retrieve an existing game from the session, it must now construct the game for every request. The downside to this approach is now the Controller has to be aware of how to construct a Board which can be given to the Game.
