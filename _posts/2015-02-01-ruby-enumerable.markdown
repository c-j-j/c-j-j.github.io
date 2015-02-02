---
layout: post
title:  "TicTacToe: Extracting the winning lines with Ruby"
date:   2015-02-01 09:20:57
categories: tictactoe ruby
---
In order for TicTacToe to work, you require some kind of mechanism to find out whether or not a player has won. I had a <code>Board</code> object with a <code>winner</code> and <code>won?</code> method to work out whether or not there was a winner.

My Board object consists of a 1D array called <code>positions</code> which keeps track of which marks are present in which position. So a 3x3 board is a 9-element array, where the first element represents the top left position, and the last element represents the bottom right position.

Initially, in order to find a winner, I had a simple loop that iterated across all possible winning configurations. For example I had the following array (of arrays), and found the winner by indexing <code>positions</code> array and checked if the same mark existed on every position;

<code>
WINNING POSITIONS = [[0, 1, 2], [3, 4, 5], [6, 7, 8], ...]
</code>

This works fine for 3x3, but if you want to have a board with an arbitrary size, this doesn't work. Instead of hard coding the winning positions, Ruby's Enumerable Module provides some powerful ways of manipulating arrays.

To extract the rows, the <code>each_slice</code> method splits the array into slices by a particular size. Here, I split it into slices where the size is the square root of the number of positions on the board (i.e. a board with 9 positions will have a row size of 3)

{% highlight ruby %}
def rows
  @positions.each_slice(Math.sqrt(@positions.size)).to_a
end
{% endhighlight %}

To extract the columns, the transpose method takes a 2d array, and swaps the rows and columns.

{% highlight ruby %}
def columns
  rows.transpose
end
{% endhighlight %}

Diagonal lines are slightly trickier, but with the <code>collect</code> method, you can build a new array to pull out the diagonal lines from <code>positions</code>

{% highlight ruby %}
def diagonal_top_left
  rows.collect.with_index do |row, index|
    row[index]
  end
end

def diagonal_top_right
  rows.collect.with_index do |row, index|
    row.reverse[index]
  end
end
{% endhighlight %}

Now that you have all of these winning lines, you can use the <code>find</code> method to find which line, if any, on the board has the same mark sitting on them. My winner method ended up looking something like this;

{% highlight ruby %}
def winner
  winning_line = winning_lines.find do |line|
    all_equal?(line)
  end

  extract_mark_from_winning_line(winning_line) unless winning_line.nil?
end
{% endhighlight %}

The result is I can create a board size with any size, and these methods will always be able to extract the correct winning lines.

This blog post only scratches the surface regarding the methods which are available in the [Enumerable](http://ruby-doc.org/core-2.2.0/Enumerable.html) module, but it is well worth a read to see what is possible.
