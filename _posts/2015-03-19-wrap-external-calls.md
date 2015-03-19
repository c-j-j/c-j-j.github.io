---
layout: post
title:  "Wrap external libraries"
date:   2015-03-19 09:20:57
categories: software
---
The other week, I was working on some JavaScript and needed to store some data on the client side, so I opted to use JQuery to save cookies rather than invent my own cookie storing mechanism.

It transpired that cookies weren't right for this task. What I needed was for each tab a user opened to have its own storage cache. 

I had to use SessionStorage instead, which does not persist data across different tabs.

I was expecting this to be a laborious task as I had used cookies extensively in my web app. Yet, it was an insanely fast task. How?

I had put the JQuery cookie methods in a separate <code>Storage</code> class, which had the following methods;

{% highlight ruby %}
def Storage 
  storeItem(key, value)
  getItem(key)
{% endhighlight %}

I only had to change two lines of production code to change the storage mechanism for the entire site.

I've done this far more lately. Wrapping any functions (third party or otherwise) that are called in several times in a class/helper function really helps when we have to change something.

Only having to change one method in one class is very satisfying and time consuming.
