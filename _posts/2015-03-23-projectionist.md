---
layout: post
title:  Vim Projectionist
date:   2015/03/23
categories: vim
---

Vim: Projectionist

A really useful plugin that I have recently been working with is [vim projectionist](https://github.com/tpope/vim-projectionist).

What makes this particularly useful is the ability to quickly navigate to what it calls are "alternate" files.

For e.g., I can open up a file lib/my_class.rb - type :A and it will navigate to spec/my_class_spec.rb. Beforehand, I would make heavy use of NERDTree/Ctrl-P to open new files.

Instead of :A, you can type :AS (open up the alternate file in a horizontal split), :AV (vertical split), or :AT (new tab).

There's other things you can do as well. If you use [vim dispatch](https://github.com/tpope/vim-dispatch), you can set default dispatch tasks.

Here is .projections.json file I have configured for a ruby project;

{% highlight json %}
{
  "*": {"make": "rake"},
  "spec/*_spec.rb": {
    "dispatch": "rspec {file}",
    "alternate": "lib/{}.rb"
  },
  "lib/*.rb": {
    "alternate": "spec/{}_spec.rb"
  }
}
{% endhighlight %}

If I'm on a file under <code>spec</code> and I run <code>Dispatch</code>, it'll run rspec on the current file. If I'm on some production code under <code>lib</code>, it'll run the dispatch task on the alternate file, which in this case will run rspec on the spec.

You can also setup tasks that start when a file is opened, i.e. <code>rails server</code> if you are working on a rails project.
