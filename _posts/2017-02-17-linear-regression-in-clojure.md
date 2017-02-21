---
layout: post
title:  Linear Regression with Gradient Descent in Clojure
date:   2017/02/17
categories: clojure linear regression machine learning
---

This blog post describes how I tackled the first exercise assignment in Andrew Ng's machine learning course on Coursera. We will be going through univariate linear regression (linear regression with a single feature), and using gradient descent to train our algorithm to find the most optimal values to provide the most accurate estimates.

My solution is hosted [here](https://github.com/c-j-j/clojure-machine-learning/blob/master/src/machine_learning/linear-regression.clj). You may wish to check out the code, start a REPL and evaluate the various expressions as we go through them. **This is one of the reasons why I'm using clojure to learn machine learning, the ability to interact with the code to understand it is second to none.**

Our data looks like the following;

{% highlight ruby %}
6.1101,17.592
5.5277,9.1302
...
{% endhighlight %}

The first column is the **population of some city**, and the second column is the amount of **profit** that can be made in that city for a food truck. The problem that we are trying to solve is given an arbitrary city's population, to predict how much profit can be made there.

The first thing we need to do is read the data from the file.

{% highlight clojure %}
(defn read-csv []
  (with-open [in-file (io/reader "src/machine_learning/ex1_data.csv")]
  (doall
   (csv/read-csv in-file))))

(def data (read-csv))
{% endhighlight %}

Let's start defining some values;

{% highlight clojure %}
(def alpha 0.01) ;; the learning rate for our algorithm
(def iterations 1500) ;; how many iterations do we run gradient descent
(def xs (map (comp read-string first) data)) ;;our feature vector, parsed as numbers
(def y (map (comp read-string second) data)) ;;our output vector, also parsed as numbers
(def number-of-examples (count xs))
(def initial-theta [0 0])
{% endhighlight %}

**Our final goal is finding the best values for `theta` to give the most accurate prediction in our algorithm.**

We need to provide our algorithm with a matrix, `X` instead of the vector xs that we currently have. We will build the `X` matrix as follows;

{% highlight clojure %}
(def X (-> [(repeat number-of-examples 1) xs]
           (matrix/matrix)
           (matrix/transpose)))
{% endhighlight %}

We'll be making use of the [core.matrix](https://github.com/mikera/core.matrix) library for our matrix operations.

The above code snippet takes our input vector, `xs`, which is `(6.1101, 5.5277)`, and converts it to a matrix where the first column are set to all ones.

{% highlight clojure %}
X
;;[[1, 6.1101]
;; [1, 5.5277]
;; ...
;; [1, 10.5277]]
{% endhighlight %}

The ones column is added for finding our value of `theta`. The reason why we add it is so we can multiple the matrices `X` and `theta`. Without the ones column, the matrix sizes mismatch.

Implementing gradient descent
----
Our main gradient descent algorithm looks like the following;

{% highlight clojure %}
(defn gradient-descent [X y theta alpha number-of-iterations]
  (loop [remaining-iterations number-of-iterations
         theta theta]
    (if (zero? remaining-iterations)
      theta
      (recur (dec remaining-iterations)
             (matrix/sub theta (cost-derivative X y theta))))))
{% endhighlight %}

We are recursively calling gradient descent until the `remaining iterations` reaches zero. During each recursive call, we are updating `theta` whereby after each iteration it becomes more accurate and provides more accurate results when it'll be used later on.

We update `theta` by subtracting the `theta` vector by the derivative of the cost function by doing `(matrix/sub theta (cost-derivative X y theta))`

To calculate the cost derivation, we define the following function;

{% highlight clojure %}
(defn cost-derivative [X y theta]
  (let [prediction (hypothesis X theta)]
    (-> prediction
        (matrix/sub y)
        (matrix/mmul X)
        (matrix/mul (/ alpha number-of-examples)))))
{% endhighlight %}

The first important step here is using our current guess of what theta is, how accurate is this when we use the current theta using the real input features, `X` and real output vector, y.

Our prediction function which takes in our input features and `theta` looks like the following;

{% highlight clojure %}
(defn hypothesis[X theta]
  (matrix/mmul X theta))
{% endhighlight %}

This is why we needed to have the ones column, otherwise this call would fail as the number of columns in X wouldn't match the number of rows in `theta`.

So if we have X defined as;

{% highlight clojure %}
[1, 6.11011]  
[1, 5.52773]
{% endhighlight %}

And `theta` defined as `[0 0]`. Then multiplying `X` by `theta` gives us a new vector with the value `[0, 0]`. If we compare `[0, 0]` with the actual values of `y`, `[17.592 9.1302]`, we can see our guess of `theta` is not producing the right guess for y. We then subtract the current value of `theta`, in this case `[0 0]`, by the value of the `cost-derivative` function. The next time we multiple `X` by `theta`, that will yield a more accurate prediction for the output.
