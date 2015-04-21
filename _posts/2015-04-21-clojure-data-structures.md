---
layout: post
title:  Clojure - Responding to Change
date:   2015/04/21
categories:clojure
---
Clojure - Responding to Change
-----------

One of the things that initially concerned me when working with Clojure is how data is spread across the entire application. For instance, with my clojure TicTacToe application, I had a <code>Board</code> data structure which was a vector as follows;

<code>[:X :X :X nil nil nil :O :O :O]</code>

To test other functions across the application that used the Board, they had to be aware that a board was indeed a vector. There is no board abstraction. I always wondered what would happen if I wanted to represent the board with another data structure, for e.g. a list? How would that change ripple across the application?

In an OO language, you would typically hide data behind some object abstraction and set it to private so that nothing else could access it. Therefore if you were to ever change the type of data stored, you can do so knowing that only the object that contains the data was referring to it. This means that change doesn't permeate across the application.

In order to have a clojure application that responds to change well, the data has to be owned by some namespace, and non-trivial operations on that data should be made available in this namespace.

For example, if I had a <code>Board</code> namespace, it'll provide an empty vector (representing an empty board) for other functions/tests to work with, and all the functions required to work on that data. All other namespaces should use functions in the <code>Board</code> namespace when working with this vector. You may choose to perform a function on the vector directly if the function is trivial and wouldn't break if the vector were to change to some other data type (for instance, nil?).

Unlike traditional OO languages, Clojure doesn't have objects to contain private data, but there's also a very small number of possible data structures that you can use. You have a hash-map/vector/list or some value (which would be strings, numbers, keywords, etc). Therefore if you were to change the data type, your choice is fairly limited.
