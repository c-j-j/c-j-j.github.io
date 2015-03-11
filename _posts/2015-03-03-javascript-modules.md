---
layout: post
title:  "JavaScript: Module Pattern"
date:   2015-03-03 09:20:57
categories: javascript module pattern
---
During the last week, I have been writing pure JavaScript for the first time. I used to rely on third-party libraries/frameworks like AngularJS and JQuery.

I was fairly happy with my initial attempt, and had used prototypes to define several objects.

However, having read the book **JavaScript: The Good Parts** and studying the JavaScript language a little more, I am now seriously unhappy with my code having reviewed it.

The major problems I have with it is that some functions are declared in the global namespace which causes odd behaviour when you have methods with the same name. My codebase is fairly small, and I still experienced conflicts with my code.

The easiest way to avoid the global namespace is to use the module pattern. I won't go into detail here on how it works, as there are far better resources out there that explain it very well indeed.

The crux of the module pattern is to use anonymous, immediately executed javascript functions that return the object that you want to create.

The following code is plagiarized from [here](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html)
{% highlight javascript %}
var MODULE = (function () {
    var my = {},
    privateVariable = 1;

    function privateMethod() {
    // ...
    }

    my.moduleProperty = 1;
    my.moduleMethod = function () {
    // ...
    };

    return my;
    }());
{% endhighlight %}

The advantage of this pattern is having objects with private methods and their own internal state.

CoffeeScript does something similar when it compiles down to JavaScript.

This is a pattern I can see myself using from now on, and does solve what is a nasty design flaw in JavaScript which is the global namespace
