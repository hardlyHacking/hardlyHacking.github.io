---
layout: post
title: My Vim Setup
date: '2014-12-16 09:50:49'
tags:
- vim
---

<h2 id="anothervimpost" name="anothervimpost">*Another* Vim Post!?</h2>

There are *far* too many articles comparing text editors, and the religious wars between strange pieces of software seem to have taken on a life of their own. For me, Vim has always worked best, though Sublime is a good second best.

Like everything else in this blog, this is a documentation of stuff I did; and magically, by writing it down, it becomes science instead of a weekend wasted.

This post is meant to be a collection of useful tips and strategies I encountered, and hopefully, some of you may benefit from these as well.

<h2 id="packagemanager" name="packagemanager">Package Manager</h2>

A package manager is a super easy way to install Vim plugins, syntax additions, indentation guidelines, colorschemes, etc. There may be more, but by far the most popular three are Tim Pope's [Pathogen](https://github.com/tpope/vim-pathogen), [Vundle](https://github.com/gmarik/Vundle.vim), and Shougo's [NeoBundle](https://github.com/Shougo/neobundle.vim).

Let me preface this section with the following: *it does not matter what package manager you use, so long as you use one*. Package managers make your life far easier, and it is well worth the time.

#### Pathogen

Historically, Pathogen was the first on the scene, and it was revolutionary. Prior to Pathogen, installing plugins on Vim was a pain. [This](http://learnvimscriptthehardway.stevelosh.com/chapters/42.html) is probably the best example of how ridiculously painful it was to install plugins. Every part of the plugin had to be placed in the correct folder!

<pre class="line-numbers"><code class="language-bash">.vim/
	colors/
	indent/
	syntax/
    docs/
    ftdetect/
    ftplugin/</code></pre>

Every file inside the plugin must be placed inside the proper ```.vim``` subfolder. If the plugin has five or six files, they better be placed in the correct folders, or strange things could occur. What a pain! And no real way to automate this!

Behold the post-Pathogen reality:

<pre class="line-numbers"><code class="language-bash">.vim/
	bundle/
    	plugin_1/
        plugin_2/</code></pre>

It's as simple as that! In fact, all you have to do is <code class="language-git">git clone</code> the plugin repository into the bundle folder, and Pathogen takes care of everything else!

So why do we have two other popular package managers?

#### Vundle

Here is an example of my <code class="language-bash">setup.sh</code> script when using Pathogen. (This is the script I that runs to setup my coding environment on a new laptop.)

```git
# Autoclose - close parens, braces, and brackets automatically
git clone https://github.com/Townk/vim-autoclose.git ~/.vim/bundle

# Autoswap - swap files in vim without errors or messages
git clone https://github.com/gioele/vim-autoswap.git ~/.vim/bundle
```

And many, many more. Quite ugly. I tried using [git submodules](http://blog.thomasupton.com/2014/02/migrating-from-pathogen-to-vundle/) for a while, but even that didn't really satisfy me. Surely, there must be a better way? Enter Vundle, the Homebrew of Vim plugins.

> Vundle is inspired by Pathogen, it can do everything pathogen can do, it is compatible with pathogen, which means if a plugin declares it supporting pathogen, it supports vundle.
>
> <cite>[Hsiaoming Yang](http://lepture.com/en/2012/vundle-vs-pathogen)</cite>

Vundle is a *superset* of Pathogen, and *none* of your plugins will break if you switch to Vundle from Pathogen. So what are some of the features that Vundle offers?

* Keep track of and configure your plugins right in the .vimrc

* Install, update, clean up, and search for configured plugins

* Search by name all available Vim scripts

* Run the above actions in a single keypress with interactive mode

What does all of this mean? I removed the portion above in ```setup.sh``` and instead added the following in my ```.vimrc```.

```vim
" Github repos
Bundle 'Lokaltog/vim-easymotion'
Bundle 'tpope/vim-fugitive'
" Vim script repos
Bundle 'FuzzyFinder'
```

Simply run ```:BundleInstall``` after launching vim, and all of these are installed! If your remove a plugin from your ```.vimrc```, simply run ```:BundleClean``` to uninstall it. You cand also update plugins in a super easy manner. All in all, Vundle is a definite improvement upon Pathogen.

#### NeoBundle

But we have a third, newly rising, package manager: NeoBundle.

This [Stack Overflow post](http://stackoverflow.com/questions/14481177/what-are-the-differences-between-vundle-and-neobundle) documents the differences between Vundle and NeoBundle quite well. Written by Shougo, it has further improvements on Vundle, including the following:

* Lazy loading of plugins - load at a user defined time, rather than at the beginning when launching vim

* An interface to work with [Unite.vim](https://github.com/Shougo/unite.vim), a popular Shougo-written plugin

* Support for [vimproc](https://github.com/Shougo/vimproc.vim), a launcher plugin

All in all, NeoBundle seems quite nifty, and has further features in addition to Vundle. Which you choose is up to you, but it is in my personal opinion that Vundle / NeoBundle is definitely better than Pathogen.

<h2 id="vimrc" name="vimrc">.vimrc</h2>

Once you've chosen a package manager, and added the necessary components into the ```.vimrc``` file to make the package manager work, there are a couple more useful things you can do.

The full file is located in the same [dotfiles repository](https://bitbucket.org/caliChander/dotfiles) as the previous blog post, [My OS X Setup](http://hackit.im/osx-dotfiles-setup/). Rather than document each and every one of my preferences, I'll just describe some of the useful tricks that help me.

#### Filetype Detect

Imaging you want both ```.markdown``` as well as ```.md``` files to open with markdown syntax. Here is an example

```vim
augroup filetypedetect
  au BufNewFile,BufRead *.markdown set filetype=md syntax=md
augroup END
augroup filetypedetect
  au BufNewFile,BufRead *.md set filetype=md syntax=md
augroup END
```

#### Windows Compatibility

Remember those darn annoying annoying ```^M``` characters when you open a file written by a PC? I can't stand it; let's get rid of it.

```vim
" Remove the Windows ^M - when the encodings gets messed up
noremap <Leader>m mmHmt:%s/<C-V><cr>//ge<cr>'tzt'm
```

#### Highlight Columns

I program in languages where 80 and 100 character columns are very important, so I highlight these columns.

```vim
" Highlight column 80 and 100
if exists('+colorcolumn')
  highlight ColorColumn ctermbg=grey
  set colorcolumn=80,100
else
  au BufWinEnter * let w:m2=matchadd('ErrorMsg', '\%>80v.\+', -1)
endif
```

#### Move Lines Easily

Moving lines up and down is something I do quite often; this is something I've found on the wonderful internets, and it has made my life much easier.

```vim
" Move current line down
nnoremap - ddp
" Move current line up
nnoremap _ ddkP
```

<h2 id="plugins" name="plugins">Plugins</h2>

Here are some of the popular plugins I've been using.

* [Multiple Cursors](https://github.com/terryma/vim-multiple-cursors): This is one of the few features that, for a long time, Sublime Text has over Vim. This plugin goes a long way to bridge that gap, though it is imperfect.

* [Easymotion](https://github.com/Lokaltog/vim-easymotion): Easy motion is a revolution in moving to any point on the page with super quickness. The gifs on the page are self-explanatory - check it out.

```vim
" EasyMotion
let g:EasyMotion_do_mapping = 0 " Disable default mappings
" Bidirectional find motion
" Jump to anywhere you want with minimal keystrokes, with just one keybinding
" `s{char}{label}`
nmap <Space> <Plug>(easymotion-s)
" or
" `s{char}{char}{label}`
" Need one more keystroke, but on average it may be more comfortable
nmap <Space> <Plug>(easymotion-s2)

" Turn on case sensitive feature
let g:EasyMotion_smartcase = 1

" JK motions: Line motions
map <Leader>j <Plug>(easymotion-j)
map <Leader>k <Plug>(easymotion-k)
map  / <Plug>(easymotion-sn)
omap / <Plug>(easymotion-tn)

" These `n` and `N` mappings are options. You do not have to map to
" EasyMotion. Without these mappings, they would work fine.
" (These mappings just provide different highlight method and some other
" features).
map n <Plug>(easymotion-next)
map N <Plug>(easymotion-prev)

map <Leader>l <Plug>(easymotion-lineforward)
map <Leader>j <Plug>(easymotion-j)
map <Leader>k <Plug>(easymotion-k)
map <Leader>h <Plug>(easymotion-linebackward)

noremap <Leader>w <Plug>(easymotion-w)

let g:EasyMotion_startofline = 0  " Keep cursor column during JK motion
```

* [Autoswap](https://github.com/gioele/vim-autoswap): Swapping files is so annoying in vim. If you already have that file open, there's a warning. And then if you save over it, you've created a duplicate. Why can't vim be smart about it? Now it can.

```vim
" Auto swap magic
set title titlestring=
```

* [Autoclose](https://github.com/Townk/vim-autoclose): Close parens, brackets, and braces automatically.

* [Tagbar](https://github.com/majutsushi/tagbar): Show classes, functions, and methods in a file, and move to them accordingly.

```vim
" Tagbar
map tt :TagbarToggle<CR>
map ta :TagbarOpenAutoClose<CR>
```

* [Tabular](https://github.com/godlygeek/tabular): This plugin is one of my favorites. [This demo](http://vimcasts.org/episodes/aligning-text-with-tabular-vim/) shows everything you need to see.

* [Syntastic](https://github.com/scrooloose/syntastic): Syntax checking for a whole host of languages; essential to make Vim more like an IDE.

```vim
" Syntastic
" Python
let g:syntastic_python_checkers = ["flake8","pyflakes","pylint"]
let g:syntastic_python_flake8_args = '--ignore="E111,E121,E123,E501"'
```

* [NerdTree Tabs](https://github.com/jistr/vim-nerdtree-tabs): Navigate directories with ease and open files with simplicity. It's helpful to alias ```nt``` to toggle nerd tree tabs. I prefer this over [Nerd Tree](https://github.com/scrooloose/nerdtree).

```vim" NerdTree
" Close vim if all that's left open is NERDTree
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") && b:NERDTreeType == "primary") | q | endif
" Map NERDTreeToggle to nt
map nt :NERDTreeTabsToggle<CR>
```

* [Ctrl-P](https://github.com/kien/ctrlp.vim): Control P is the latest and greatest in navigating between buffers like a breeze. It has changed my life.

```vim
set runtimepath^=~/.vim/bundle/ctrlp.vim
let g:ctrlp_clear_cache_on_exit=0
```

* [Powerline](https://github.com/powerline/powerline) is a better status bar for Vim - it displays what mode you're in, the file name, and just looks way better.

```vim
" PowerLine
set rtp+=/Users/chanchan/Library/Python/2.7/lib/python/site-packages/powerline/bindings/vim

if has("gui_running")
   let s:uname = system("uname")
   if s:uname == "Darwin\n"
      set guifont=Meslo\ LG\ S\ for\ Powerline
   endif
endif

set encoding=utf-8 " Necessary to show Unicode glyphs
set nocompatible   " Disable vi-compatibility
set laststatus=2   " Always show the statusline
```

<h2 id="resources" name="resources">Resources</h2>

Want to learn more about Vim, Vimscript, plugins and shortcuts? Here are some of the resources that I find best.

* [Grok Vi](http://stackoverflow.com/questions/1218390/what-is-your-most-productive-shortcut-with-vim/1220118#1220118) - probably the single most discussed Stack Overflow Vim post; a gentle reminder that while vim has some incredible new features and shortcuts, understanding basic vi, and the features that made it so powerful, are absolutely essential.

* Vim After 11 Years [Part One](http://statico.github.io/vim.html) and [Part 2](http://statico.github.io/vim2.html) - a fantastic collection of useful plugins, links to discussions on Hacker News as well as Reddit, and just general tips and suggestions.

* Learning Vim script is hard. Unfortunately, sometimes the hard way is the best way. [Steve Losh's book](http://learnvimscriptthehardway.stevelosh.com/) is the single best one I've come across - it starts from the most basic and gets wicked awesome. And along the way, you'll learn Vim like a pro.

* Of *course* if you love Vim, you simply must love our lord and savior [Tim Pope](https://github.com/tpope) who has some of the best Vim plugins ever. His open source work and contribution will forever shape office productivity. :wq Amen.

* [Damian Conway's seminar](https://www.youtube.com/watch?v=aHm36-na4-4) on writing Vim plugins.

* Tabs are *not* buffers. [Here](https://joshldavis.com/2014/04/05/vim-tab-madness-buffers-vs-tabs/) are [two](http://stackoverflow.com/questions/102384/using-vims-tabs-like-buffers) sources that explain the difference between tabs and buffers. This is likely one of the most misunderstood topics in all of vim-dom. Learn it. Love it. Embrace it.

Happy hacking - and remember to keep flying Vim!