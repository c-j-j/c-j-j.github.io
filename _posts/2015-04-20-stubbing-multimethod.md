---
layout: post
title:  Clojure - Stubbing Multimethod
date:   2015/04/20
categories: clojure
---
Clojure - Stubbing Multimethod
----------------
One way to achieve polymorpism in Clojure is to use [multimethods](http://clojure.org/multimethods).

With my TicTacToe application in Clojure, I have two different types of players, Human and Computer.

To do the polymorphic dispatch, I have a dispatch method which picks out the <code>player_type</code> value from the hash-map that is passed into it. For e.g, if I pass in <code>{:player_type :human}</code>, it'll look for the multimethod which has <code>:human</code> label.

{% highlight clojure %}
(defmulti get-move (fn [player-types board]
                         (player-types :player_type)))
{% endhighlight %}

In order to call the method, you'd do something like this;

{% highlight clojure %}
player/get-move {:player_type :human} board
{% endhighlight %}

This will eventually delegate the method call to the following;

{% highlight clojure %}
(defmethod player/get-move :human [_ board]
  (get-move-logic-goes-here board))
{% endhighlight %}


I think this is really nice.

Elsewhere, I have a <code>play-game</code> function that runs through the entire game and keeps track of the current player.

However, I don't want to use my human-player code, nor do I want to use my computer-player code. I want to use a stub player instead, so I can control what the next move will be placed.

In my unit test, I declare a new method that my multimethod can dispatch to;

{% highlight clojure %}
(defn prepare-stub-player-move [next-move]
  (defmethod player/get-move :stub-player [_ board] next-move))
{% endhighlight %}

In order to use a stub player, firstly I pass in a player to my <code>play-turn</code> function with the player_type <code>stub-player</code>.

In the unit test, prior to calling <code>play-turn</code>, I just call <code>(prepare-stub-player-move some-move)</code> to get the stub player to always return <code>some-move</code>.

There we have it, stubbed multimethods in Clojure.
