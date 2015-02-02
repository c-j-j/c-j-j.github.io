---
layout: post
title:  "Vim: Refactoring class & method names"
date:   2015-02-02 09:20:57
categories: vim refactoring
---
I'm going to be honest with you, I switched from using IntelliJ to vim nearly a month ago, and there are still things I miss on an IDE. What makes IntelliJ so powerful is the ability to refactor and rename class/method names with one shortcut key. Admittidly, Java makes this easier for the IDE than a dynamic language like Ruby due to its static typing. However when you're accustomed to an IDE doing all of the work for you, it can feel like an uphill struggle to switch over to vim.

This post will be fairly short, and will cover a workflow I've found to work reasonably well when it comes to renaming class and method names in Ruby. Before we continue, I am aware that a lot of developers do successfully use sed to rename classes/methods. However I didn't particularly like sed for two reasons;

1. You need to be careful when changing method names that are fairly common. For this reason, I sometimes want to check before each replacement. I'm assuming this is possible with sed, but I never got to that point. 
2. I didn't like having to come out of vim to edit the text that I am currently working with. It means I have to copy a method name to the clipboard, and paste that into sed later on.

The workflow that I am currently using is as follows, and works for renaming class and method names. Lets rename a method called 'some_method' to 'new_method';

1. Using the [fugitive](https://github.com/tpope/vim-fugitive) plug-in, place the cursor over the method you want to replace, and type <code>:Ggrep Control-R Control-W></code>. This uses git grep to search in your project for a particular string. Control-R Control W pastes in the word currently under the cursor. Quick tip: Create a keyboard mapping in your vimrc to do this, it'll save you time later on.

2. Ggrep populates the quickfix list with all references to the string you just searched for. With the [qargs](https://github.com/nelstrom/vim-qargs) plugin installed, type <code>:Qargs</code>. This will copy the Quickfix list into the vim arg list.

3. Next, we will use the argdo command to perform a command on all files in the args list. Type the following to do the substitution <code>:argdo %s/some_method/new_method/gc</code>

4. The 'c' flag at the end is important, as you can decide whether or not you want to rename a particular string. Use the instructions prompted in vim to decide whether a particular string should be subsituted. 

To rename a class, you first can follow the steps above the rename the ClassName and class_file_name, and then do the following to move the file such that git will track it;

1. Go to the file you want to rename

2. Gmove path/to/destination.rb


