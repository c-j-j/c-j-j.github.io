---
layout: post
title:  "A tip to improve your vim"
date:   2015-03-17 09:20:57
categories: vim
---
I was doing the coin change kata the other week, and one of the bits of feedback I received was that my Vim skills needed work.

I thought my vim skills were okay, but I was actually using the H,J,K,L keys far too much. I was traversing documents just using k (up) and j (down) keys.

Now, I have configured my Mac to repeat keys quickly when I hold the key down. However, vim provides many other ways to navigate around a document.

So how do you get out of the habit of using H,J,K, L? It's easy, disable those keys (temporarily). Now you can't use them, you are forced to find other habits.

<code>nnoremap h <nop></code>
<code>nnoremap j <nop></code>
<code>nnoremap k <nop></code>
<code>nnoremap l <nop></code>

And habit is the key word here. I disabled these during a Friday afternoon, and I quickly had to change habits that I have since kept.

These are crucial keys, it's not practical to never use these keys. However they should only be used for one-off movements.

Instead of using these keys, use;

1. f/F to move to a character on a line
2. w/b to move back and forward a word
3. G5 to move to the 5th line, or use :5<CR>
4. {,} to move one paragraph (which usually translates to a function)
5. /,? to find text to navigate to
