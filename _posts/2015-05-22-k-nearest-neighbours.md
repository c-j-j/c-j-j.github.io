---
layout: post
title:  Machine Learning - K-Nearest Neighbours
date:   2015/05/22
categories: machine learning
---
Machine Learning - K-Nearest Neighbours
------------

Classification vs Regression
=================
Before we begin, lets get some terminology out of the way.

- Machine Learning: is essentially a piece of software that is able to receive some data and is able to make predictions using this data. The main types of algorithms used in machine learning are classification and regression.
- Classification algorithms - Could something be this or that? If you were to apply for a mortgage and you gave the bank a load of your criteria (age, occupation, etc), they will say YES or NO as to whether or not they'll lend you the money. The result of a classification algorithm is one of a set of possible outcomes.
- Regression algorithms - What would be the value of this? If you had the heights/weights of 100 people, and plotted that data onto a graph, you could work out roughly guess how much a different person with a particular height would weigh. The result of a regression algorithm is typically a value that is calculated.

K-Nearest Neighbours
===============
To describe what K-Nearest Neighbours is, let us use an an entirely plausable real world analogy. You are magically teleported to another part of the world, and are to continue your career as a software craftsmen there. However, during the teleportation process, you have somehow lost all of your previous programming knowledge. What a pity.

Furthermore, the teleportation process has eradicated your ability to know multiple programming languages. You must now select one programming language and you'll need to find a job with this single skill.

Which programming langauge would you learn to maximise the chance of you finding a new job? What you may do is find out what language most of the companies in the local area are using, and learn that. How many of the nearest companies do you look at? 1? 2? K? You may see where I'm going with this. If 4 out of the 5 nearest companies in your area are all using Java, you may come to the realization that Java may be your best langauge to learn in order to find a new job. Like I said before, we are using an entirely plausable example here.

What I've described here is a Classification algorithm, whereby you are selecting which class (or in this case, programming langauge) to choose from.

K-Nearest Neighbours is split into two varieties, classification and regression.

K-Nearest Neighbours Classification works as follows;

Given the following graph;

![image](http://www.statsoft.com/portals/0/Support/KNNOverViewImageA.jpg)

1. Select a query point on the graph (red point on the graph)
2. Select k number of data points that are closest to the query point.
3. We can determine that the value of the query point will likely be the most common that the other data points hold.

K-Nearest Neighbours Regression works in a similar way. Instead, rather than using the most common value in the neighbourhood, you would typically use the average of the values in the neighbourhood instead.

This algorithm can be applied in lots of dfferent areas. If you had a load of medical data for blood tests, you may expect a group of people with a particular disease to be clustered together if you were to plot that data in a graph.
