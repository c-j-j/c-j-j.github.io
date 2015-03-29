---
layout: post
title:  Developing C# with vim on OSX
date:   2015/03/25
categories: vim
---

Developing C# with vim on OSX
--------------
This guide will explain how I have setup a C# development environment using vim on OSX.

Environment Setup
===============
The following need to be installed onto your computer beforehand.

1. [Mono](http://www.mono-project.com/)

2. Python 2.7


Vim setup
==============

We'll be heavily using [omnisharp-vim](https://github.com/OmniSharp/omnisharp-vim#installation) in this guide.. This plugin provides a huge amount of features that facilitates C# development. To make full use of the features that omnisharp has to offer, the following plugins are needed;

3. [ctrlp](https://github.com/kien/ctrlp.vim)

4. [Syntastic](https://github.com/scrooloose/syntastic)

5. [vim-dispatch](https://github.com/tpope/vim-dispatch)

6. [supertab](https://github.com/ervandew/supertab)


Configuring omnisharp to run tests with nunit
=========================
From the time of writing this, omnisharp is configured to expect <code>nunit-console.exe</code> in your path somewhere. If this isn't on your path, the nunit tests will silently fail to run. You have a choice with how to fix this;

1. Navigate to the omnisharp plugin configuration and change the test command from using <code>nunit-console.exe</code> to <code>nunit-console4</code>.
2. In a directory that is on your path, create a symlink from nunit-console.exe to /Library/Frameworks/Mono.framework/Commands/nunit-console4

Personally, I prefer the second option as it gives me the freedom to reinstall the plugin if necessary without having to dig into the configuration. Furthermore, the [location](https://github.com/OmniSharp/omnisharp-server/blob/0fc2fab6c44a19c6555248aa903c1e076afbd865/OmniSharp/config.json#L10) where the config.json is said to be stored is now out of date, and it is unclear which config.json in the plgunis directory structure you should use.

Configuring vim with omnisharp shortcuts
=========================
You now have everything needed to open up a C# project in vim and be able to invoke omnisharp functions. However, you'll need to make some amendments to your vim setup first to add some useful shortcuts. I would advise that you create a [file type plugin](http://vim.wikia.com/wiki/File_type_plugins) to dump this configuration into, so you can keep your .vimrc devoid of all of this c# configuration. Here's what I've used, which I've yanked and slightly modified from [here](https://github.com/OmniSharp/omnisharp-vim#example-vimrc) to improve performance. 

{% highlight vim %}
"OmniSharp won't work without this setting
filetype plugin on

set tabstop=4
set shiftwidth=4
set expandtab

"Showmatch significantly slows down omnicomplete
"when the first match contains parentheses.
set noshowmatch

"Super tab settings
let g:SuperTabDefaultCompletionType = 'context'
let g:SuperTabContextDefaultCompletionType = "<c-x><c-o>"
let g:SuperTabDefaultCompletionTypeDiscovery = ["&omnifunc:<c-x><c-o>","&completefunc:<c-x><c-n>"]
let g:SuperTabClosePreviewOnPopupClose = 1

"don't autoselect first item in omnicomplete, show if only one item (for preview)
"remove preview if you don't want to see any documentation whatsoever.
set completeopt=longest,menuone,preview

"Move the preview window (code documentation) to the bottom of the screen, so it doesn't move the code!
"You might also want to look at the echodoc plugin
set splitbelow

" Get Code Issues and syntax errors
let g:syntastic_cs_checkers = ['syntax', 'semantic', 'issues']

"Set autocomplete function to OmniSharp (if not using YouCompleteMe completion plugin)
set omnifunc=OmniSharp#Complete

" Synchronous build (blocks Vim)
nnoremap <F5> :wa!<cr>:OmniSharpBuild<cr>
" Builds can also run asynchronously with vim-dispatch installed
nnoremap <leader>b :wa!<cr>:OmniSharpBuildAsync<cr>
" automatic syntax check on events (TextChanged requires Vim 7.4)
autocmd BufWritePost *.cs SyntasticCheck
autocmd BufWritePost *.cs SyntasticSetLoclist

" Automatically add new cs files to the nearest project on save
autocmd BufWritePost *.cs call OmniSharp#AddToProject()

"The following commands are contextual, based on the current cursor position.
nnoremap gd :OmniSharpGotoDefinition<cr>
nnoremap <leader>fi :OmniSharpFindImplementations<cr>
nnoremap <leader>ft :OmniSharpFindType<cr>
nnoremap <leader>fs :OmniSharpFindSymbol<cr>
nnoremap <leader>fu :OmniSharpFindUsages<cr>
"finds members in the current buffer
nnoremap <leader>fm :OmniSharpFindMembers<cr>
"cursor can be anywhere on the line containing an issue
nnoremap <leader>x  :OmniSharpFixIssue<cr>
nnoremap <leader>fx :OmniSharpFixUsings<cr>
nnoremap <leader>tt :OmniSharpTypeLookup<cr>
nnoremap <leader>dc :OmniSharpDocumentation<cr>

nnoremap <leader>rt :OmniSharpRunTests<cr>
nnoremap <leader>rf :OmniSharpRunTestFixture<cr>
nnoremap <leader>ra :OmniSharpRunAllTests<cr>
nnoremap <leader>rl :OmniSharpRunLastTests<cr>
nnoremap <leader>u <nop>

" Remove 'Press Enter to continue' message when type information is longer than one line.
set cmdheight=2

" Contextual code actions (requires CtrlP or unite.vim)
nnoremap <leader>ca :OmniSharpGetCodeActions<cr>
" Run code actions with text selected in visual mode to extract method
vnoremap <leader>ca :call OmniSharp#GetCodeActions('visual')<cr>


" rename with dialog
"nnoremap <leader>nm :OmniSharpRename<cr>
"nnoremap <F2> :OmniSharpRename<cr>:wa<cr>
nnoremap <F2> :OmniSharpRename<cr>
" rename without dialog - with cursor on the symbol to rename... ':Rename newname'
"command! -nargs=1 Rename :call OmniSharp#RenameTo("<args>")

" Force OmniSharp to reload the solution. Useful when switching branches etc.
nnoremap <leader>rl :OmniSharpReloadSolution<cr>
nnoremap <leader>cf :OmniSharpCodeFormat<cr>
" Load the current .cs file to the nearest project
nnoremap <leader>tp :OmniSharpAddToProject<cr>

" (Experimental - uses vim-dispatch or vimproc plugin) - Start the omnisharp server for the current solution
nnoremap <leader>ss :OmniSharpStartServer<cr>
nnoremap <leader>sp :OmniSharpStopServer<cr>

" Add syntax highlighting for types and interfaces
nnoremap <leader>th :OmniSharpHighlightTypes<cr>
"Don't ask to save when changing buffers (i.e. when jumping to a type definition)
"
"extract text selected in visual mode as variable
vnoremap <leader>ev cvariable<esc>O<esc>pIvar variable = <esc>A;<esc>==w<F2>
set hidden
{% endhighlight %}

Setting up a C# project
==================
To setup a C# project without relying on visual studio/xamarin studio, we can use [grunt-init-csharpsolution](https://github.com/nosami/grunt-init-csharpsolution).

Creating a project is then as straight forward as navigating to an empty directory, and performing the following;

1. <code>grunt-init csharpsolution</code>

2. <code>nuget restore</code> to install any dependencies

What functionality has been added
==================
Now that we have this all setup, what does all of this give us? Provided you have followed all of these instructions, we now have

1. Tab Autocomplete, which also shows which parameters a particular function expects.

2. Project wide refactor. Press F2 to change a class/function/variable name and it'll rename all instances in your project.

3. Asynchronous test runner. In a nunit test, either press <code>,ra</code> to run all tests, <code>,rf</code> to run all tests in a particular fixture, or <code>,rt</code> to run a single test. 

4. Context aware code actions. If you have configured Syntastic to work, you can hover over code shaded red/blue (which is Syntastic that indicating something is wrong), <code>,ca</code> will provide ways you can fix this.

5. Navigate to classes/functions using <code>gd</code>.

Conclusion
=================
These features have allowed me to come up with a pretty decent workflow for writing C# using vim. I would strongly recommend that you read through the omnisharp documentation to find out everything this great plugin has to offer.
