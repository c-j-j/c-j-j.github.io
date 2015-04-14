---
layout: post
title:  Doing the simplest thing possible
date:   2015/04/14
categories: software development
---
Doing the simplest thing possible
----------------
Since starting at 8th Light, there has been a strong emphasis on doing things the simplest possible way. This notion applies to many different areas of software engineering.

When test driving a new piece of functionality, it is best to get into the habit of initially doing the simplest thing that can possible work. This can be challenging, as you will undoubtedly have an idea in your head how the code will look, and it is tempting to skip the trivial tests.

Recently, I have been learning Clojure and writing my first TicTacToe application with it. Clojure is radically different from any other programming language that I've used thus far. It's been difficult to get used to. At one point, I was attempting to come up with a <code>Player</code> abstraction that can be dependency injected into a <code>Game</code> class. I had done the same in Ruby and C# with ease, and has allowed me to inject either Human/Computer/Stub players.

I really struggled getting this to work in a functional way, and struggled even more so trying to test this. After being stuck for some time, a colleague said to throw away the Player abstraction, hard code the player functionality into the game class, and refactor later. This was way easier to achieve, and despite violating SRP I had a working solution that can be cleaned up later on. "Make it work, then make it clean.".

I did the rest of the TicTacToe with the approach whereby I would just do the simplest thing possible, and I quickly cobbled together a game that was playable. Now there were issues and plenty of things to improve upon, but having a basic working game is a great thing to start with.

Now that's all well and good for something relatively basic like TicTacToe, but how does this approach work in the real world? In the past when I've joined software projects that are in their maturity and have a huge amount of complexity, I always wondered how such a system was ever designed and when all of this complexity was added.

At the start of a software project, XP says that you should pick the most important stories that will force you to come up with the basic architecture that you want to work with. It also says that you only write the functionality that'll make the selected stories work, and nothing else. This sounded a bit paradoxical initially, surely you want to come up with an architecture whereby new functionality can be easily added, and therefore you'd like to create the abstractions early on that'll would make future changes trivial to introduce.

It turns out that those preemptive abstractions are costly;

1. Coming up with the abstractions takes more effort.

2. Having them in your application adds complexity.

3. You are not certain what changes exactly you'd need in the future.

4. Having leaky abstractions can cause future code to be coerced into using it, and could result in less elegant solutions.

If you keep code simple and clean, it will be easy to change. Trying to preempt change can at times be necessary if you think it will save time in the future, but usually it should be largely avoided.
