---
layout: post
title: My OS X Setup
date: '2014-12-04 22:29:43'
tags:
- os-x
- productivity
---

<h2 id="motivations" name="motivations">Motivations</h2>

I was heavily inspired by [mganjoo](https://twitter.com/mganjoo)'s dotfiles [repository](https://github.com/mganjoo/dotfiles), which has some of the most useful plugins and features I've seen. Putting all the dotfiles, terminal themes, and environment stuff into a signle dotfiles repository that's version controlled is, obviously, super useful. It means that setting up on a new computer is trivial, and within minutes, you're back into your highly optimized dev environment.

My hope with this blog post is to give people the same insight to others that I received along with pointing and sharing some of the useful programs that I've found along the way, and encouraging you all to set up something similar. If you want to just look at the code, checkout my [Bitbucket repo](https://bitbucket.org/caliChander/dotfiles).

<h2 id="homebrew" name="homebrew">Homebrew</h2>

```bash
brew install python
```

You should install all applications, command line or mac applications, with [Homebrew](http://brew.sh/) if you can. This is pretty much  universal installation mechanism for all packages; it's important to avoid installing things without Homebrew, as Homebrew can be quite sensitive about this and things can go wrong. Installing Homebrew itself is simple enough, and should be one of the first things you do when opening up a new Mac.

Here are a list of the most awesome Homebrew packages that I use on a daily basis.

**Autojump**

[autojump](https://github.com/joelthelion/autojump) is the new, awesome way to navigate between directories like a breeze. It does depend on caching, so you can't use autojump on a directory you haven't yet visited, but once you start using it, it adapts to your usage patterns.

![](/images/2015/08/Autojump.gif)

Keep in mind that to make autojump work, you want to add the following to your "$SHELL".rc file.

```bash
[[ -s `brew --prefix`/etc/autojump.sh ]] && . `brew --prefix`/etc/autojump.sh
```

**fzf Fuzzy Finder**

[fzf](https://github.com/junegunn/fzf) fuzzy finder has forever changed the nature of reverse history search. This is a fuzzy finder that has an awesome text interface tied with it, which makes finding obscure past history commands a breeze, even if you don't exactly remember them.

**ag the Silver Searcher**

[ag](https://github.com/ggreer/the_silver_searcher) the silver searcher is the really, *really* fast alternative to grep. And remarkably enough, it doesn't do any caching - it's just absurdly fast. Seriously. Just try it.

I did the following test on a source code repository that was 23 GB, and ag came out *twenty* times faster.

```bash
grep --color=auto --exclude-dir={.bzr,.cvs,.git,.hg,.svn} -r "README" source  100.99s user 11.36s system 79% cpu 2:21.00 total
ag "README"  5.56s user 7.20s system 197% cpu 6.462 total
```

**Better Diff**
[wdiff](http://www.gnu.org/software/wdiff/) compares files on a word per word basis, where words are defined as anything between whitespace. This is useful on select occasions.

But by far more useful is [colordiff](http://www.colordiff.org/), which does exactly what it sounds. No more columns, not more indentation, and no more wierd carats on either side. Just plain, easy breezy colors.

**peco**

Have you ever been in the middle of a series of pipes and wished that you could interactively select an option, and then feed it to the rest of the pipes? [peco](https://github.com/peco/peco) is your answer. The interface is beautiful, and you can navigate inside of peco just like ```vi``` (or ```less```).

![](/images/2015/08/Peco.gif)

<h2 id="caskroom" name="caskroom">Caskroom</h2>

```bash
brew cask install firefox
```

[Caskroom](http://caskroom.io/), on the other hand, is less well known than Homebrew. It's a moule for Homebrew that allows you to install Mac applications like Firefox and Chrome through the command line, which is super awesome. No more 'click and drag this icon' business, and certainly no more searching around for where this application can be found. You now have a single resource for finding and installing almost all Mac applications.

Like Homebrew, it works best if you use Caskroom for installing all Mac applications - avoid the non-Caskroom solutions as much as possible. On occasion, however, you'll find packages that aren't part of Caskroom, especially when it's after a recent release. Caskroom is community driven, so help the community out and [add a cask](https://github.com/caskroom/homebrew-cask/blob/master/CONTRIBUTING.md) - it's really quite easy.

Some of my favorite applications.

**Quicksilver**

[Quicksilver](http://qsapp.com/) is so popular that by now it needs to introduction. But it's been a favorite of mine for many years, so it deserves special mention.

**iTerm2**

We'll have a section on this later, but you should certainly install it with Cask and not from the actual website.

**Bartender**

If you're like me, you have tons of applications at the top menu bar open. I have 1Password, Caffeine, Divvy, Wifi, Bluetooth, Battery, Notifications, Spotlight, Time Machine, Volume, Quicksilver, VPN, JiTouch, Evernote, Dropbox, and the list goes on.... and on...

[Bartender](http://www.macbartender.com/) has been a life saver - it cleans up my screen and makes organization a breeze. Note that it is not free, though it has a lengthy trial run.

**LightPaper**

I like dealing with most things in Markdown, and [LightPaper](http://www.ashokgelal.com/lightpaper-for-mac/) is a super easy way for me to visualize and edit in Markdown actively.

**Divvy**

![](/images/2015/08/Divvy.gif)

[Divvy](http://mizage.com/divvy/) is one of thos productivy porn must-haves. It easily allows me to resize windows. I've mapped the Divvy shortcut to ```Control-Space```, as I have Quicksilver using ```Command-Space```. The best part is it works easily with multiple monitors. For someone with OCD like me, I want my sizing to be in perfect squares, and Divvy ensures that.

**JiTouch2**

The trackpad is one of the best features about the Mac. Unlike when using a PC, I've never felt the need to have a mouse on a Mac. But it's time to take multigesture to the next level with [JiTouch2](https://www.jitouch.com/). You can change tabs, close tabs, reopen the most recent tab, minimize, maximize, resize, and do a whole lot more with complex gestures.

<h2 id="zsh" name="zsh">Zsh</h2>

What flavor of [ZSH](http://www.zsh.org/) you use is up to you, but I do recommend switching over from BASH unless you absolutely cannot due to some dependency or other. In general, ZSH is a superset of BASH, and most BASH scripts can even run as is. However, I find there are a couple key features that sell me on ZSH:

* Awesome tab completion that is context sensitive (tab on a `kill` command will actually show you which processes to kill)
* zmv - regex in moving and renaming files
* Auto expand PATH variables
* Better / extended globbing
* Global aliases

I had fought for a long time between [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) and [prezto](https://github.com/sorin-ionescu/prezto). For a long time I think prezto was faster, leaner, and better performant, but recently I've found this is not the case. oh-my-zsh has caught up significantly, and overall out of the box, it's definitely a better environment for me. In fact, ever since I stopped using git and switched to mercurial, I can't notice a difference at all, and I find any minor discomfort is outweighed by the variety of plugins and themes.

Couple comments on the ZSH features that I love specifically.

**History**

I *hate* long and repetitive histories that I have to do complex searches on. If I have a command I find useful, I expect to have it in my history and be easily found with a simple reverse search (or forward search). To aid me, I've added some useful settings:

* Ignore super simple default commands that are issued quite often

```bash
export HISTIGNORE="&:ls:[bf]g:exit:reset:clear:cd:cd ..:cd..:clear"
```

* Make my history much longer

```bash
export HISTSIZE=25000
export SAVEHIST=25000
```

* Definitely ignore all duplicates and other repetitions

```bash
setopt HIST_IGNORE_ALL_DUPS
setopt HIST_IGNORE_SPACE
setopt HIST_REDUCE_BLANKS
```

**Fish Shell**

![](/images/2015/08/fish-syntax.png)

The [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) plugin brings the awesome Fish-shell style syntax to zsh! It's very awesome, and to activate it simply include the following near the end of your .rc file.

```bash
plugins=(zsh-syntax-highlighting)
source $ZSH/oh-my-zsh.sh
```

Remember to put it at the end of the plugins list.

**Prompts**

Version control plugins are seriously awesome. Having ZSH tab complete in an intelligent manner what you want to do is great. This is particularly useful when configuring [awesome prompts](http://mikebuss.com/2014/02/02/a-beautiful-productive-terminal-experience/), and the blog post I've linked does it far better than I could hope. One word of caution though: Although these prompts are awesome, when dealing with extremely [large ~~git~~ repositories](http://jijoejv.wordpress.com/2012/06/22/workaround-slowness-of-oh-my-zsh-git-plugins-on-large-repositories/), there are [known issues](http://stackoverflow.com/questions/12765344/oh-my-zsh-slow-but-only-for-certain-git-repo) of every command taking a [long time](http://marc-abramowitz.com/archives/2012/04/10/fix-for-oh-my-zsh-git-svn-prompt-slowness/comment-page-1/). So be careful.

<h2 id="iterm2" name="iterm2">iTerm2</h2>

[iTerm2](http://iterm2.com/) is the popular terminal replacement for OS X. It has several features that are better than vanilla Terminal.app:

* Command-D makes vertical screen split
* Command-Shift-D makes horizontal screen split
* Can record up to a configurable number of seconds - ever had something on the screen for an instant, and, for whatever reason, you can't recover it? Just rewind!
* Awesome themes

See their website for a series of posts explaining how much better they are than the regular app. And they're free, so there's really no reason not to give it a try. (Donate if you like it!)

I like to have a folder within my dotfiles dedicated to my various iTerm2 themes. Some of the ones I like are [Argonaut](https://github.com/effkay/iTerm-argonaut), [IRBlack](https://gist.github.com/meqif/1238378), and the various permutations of the [Tomorrow](https://github.com/ChrisKempson/Tomorrow-Theme) theme.

<h2 id="versioncontrol" name="versioncontrol">Version Control</h2>

Now that you've done all this hard work, be sure to add your setup dotfiles directory to version control! Whichever you choose is fine. This should be obvious, but too many of the students that I TA-ed over the years forgot this part; there's no point in having a recovery setup procedure, if you don't secure the setup procedure in version control.

I ended up making a single ```setup.sh``` script to handle the entire homebrew, cask, zsh setup process. This way, I only had to check in four things (besides the setup script) to version control:

* iTerm2 themes folder
* zshrc
* vim folder
* vimrc

Anything else like a gitconfig or hgrc is optional, and could be done as well. To modularize the setup script, I created two functions to install a brew package and a cask package respectively.

```bash
# Install a homebrew package if it's not already installed
# Takes between 1 - 3 arguments
# 1 argument: executable / homebrew package name
# 2 argument: executable name, homebrew package name
# 3 argument: executable name, which brew to tap, brew package name
function brew_install {
   # Bad case - too few arguments
   if [[ $# -lt 1 ]]; then
      echo "ERROR: function brew_install has too few arguments"
   # Function and brew package have the same name
   elif [[ $# -eq 1 ]]; then
      which -s "$1" || brew install "$1"
   # Function and brew package have different names
   elif [[ $# -eq 2 ]]; then
      which -s "$1" || brew install "$2"
   # Need to tap before brew installing
   elif [[ $# -eq 3 ]]; then
      which -s "$1" || (brew tap "$2" && brew install "$3")
   # Bad case - too many arguments
   else
      echo "ERROR: function brew_install has too many arguments"
   fi
}
```

And for the caskroom version:

```bash
# Install a cask application if it's not already installed
# Function takes 1 argument: cask package name
function cask_install {
   if [[ $# -lt 1 ]]; then
      echo "ERROR: function cask_install has too few arguments"
   elif [[ $# -eq 1 ]]; then
      output=`ls /opt/homebrew-cask/Caskroom/ | grep "$1"`
      if [[ -z "$output" ]]; then
         brew cask install "$1"
      fi
   else 
      echo "ERROR: function cask_install has too many arguments"
   fi
}
```

<h2 id="finalthoughts" name="finalthoughts">Final Thoughts</h2>

Making this post itself was quite a useful exercise for me, just so I could finally clear out many of the old plugins I no longer use, fix up and clean much of the old code before pushing it to BitBucket for many other eyes to see, and ultimately just moving my lazy butt to actually do it!

Sure enough, it paid off. Having set this up, I moved jobs just days afterwards, and all of this installed on my new work computer like a breeze. I was up and running in less than an hour!

I've decided to make another blog post along the same idea bout the general hacking environment I've set up. In particular, there are a couple things I specifically excluded because they weren't OS X specific.

* **Vim**: Besides MacVim, there's not much to talk about here; besides,this alone could eclipse this entire blog.
* **Tmux / Screen**: I have both a screenrc and a tmux.conf, but I haven't yet put them up to the repo. As I prepare for the next blog post, I'll likely do so
* **Version Control**: The gitconfig and hgrc are also to follow in the next blog post

I hope this has been helpful. Feel free to share some of your favorite applications and packages. Happy hacking!
