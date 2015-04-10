---
layout: post
title:  Clojure First Impressions
date:   2015/04/09
categories: clojure
---
Clojure First Impressions
----------------

I've been working on Clojure for the last couple of days, and thus far it has made a big impression on me.

I started off with the Clojure Koans, which I didn't find particularly helpful. I found the nature of filling in the blanks a little too easy and not very educational.

After this, I did several of the the [4Clojure](https://www.4clojure.com/) problems, which I found far more helpful in learning a new language, as you need to think a little more about what you are doing.

With a few examples of 4Clojure done, I started on my TicTacToe and as I've done in the other OO programming languages, my first unit test was to create a Board object. I quickly realised how silly this was in a functional language, as there are no constructors in Clojure which I'm used to. In fact, although Clojure does give some options to work with state (i.e. atoms/refs), it seems the convention to not use instance variables that have their own state.

I'm not so sure about this approach, as I'm so used to working with objects that contain state. It seems like I'm working with a bunch of static classes/methods in an OO language.

However, because Clojure gives you so much flexibility when it comes to composing functions, it makes data feel like play-doh where I can mangle it anyway I please. This means that those 'static' methods can do a huge amount with a small amount of code.

I'm using Midje as a testing library, and I'm finding it an excellent way of learning the language. It runs tests on file changes and displays readable output with colour, but it also has a REPL which I feel really helps improve workflow.

With the OO languages I am used to working with, I strived towards having data behind some abstraction and only being able to access that data through methods on an object. That gives me the option to change the underlying data structure without affecting clients of that object.

With Clojure, the data (hash maps, vectors, lists) are piped across the application instead. I'm concerned that changes will ripple across the application.

I'm really looking forward to learning more about this language, and will post an update to see if my concerns that I've posted here were short-lived.
