---
layout: post
title: My TMux Configurations
date: '2015-06-06 21:52:22'
tags:
- tmux
---

## My TMux Setup

TMux has been a game changer. Unlike screen, I've found the need for very little tinkering, and it works quite well right out of the box.

That said, it's not perfect, so here are a few small changes I made.

### New Prefix

I hated Control-A for my prefix during my GNU Screen days, as most terminals use Emacs configurations, and Control-A is a quick-skip to the front of the command line, which is very useful. That said, Control-B is nearly as awful, since it's so far away hard to hit. So I set it to Control-S, as I don't use that for anything really.

```nolanguage
set-g prefix C-s
unbind-key C-b
bind-key C-s send-prefix
```

### Swapping Windows

Swapping windows has got to be easier. Why worry about whether a slot is taken by another window or not? Just do it, right?

```bash
bind-key -n C-S-Left swap-window -t -1
bind-key -n C-S-Right swap-window -t +1
```

### Colors

Pretty self explanatory.

```bash
set -g default-terminal "screen-256color"
```

### Renumbering / Renaming

Start numbering at 1, please.

```bash
set -g base-index 1
```

Renumber windows if they move around.

```bash
set-option -g renumber-windows on
```

Stop renaming after I rename them.

```bash
set-option -g allow-rename off
```

### UTF8

Handle it.

```bash
setw -g utf8 on
```

### Restoring from Reboot

There is no native way to restore a detached TMux session after a reboot / shutdown. So the solution I'm presenting here is shamelessly stolen from a [StackOverflow post](http://superuser.com/questions/440015/restore-tmux-session-after-reboot).

This [bash script](https://github.com/mislav/dotfiles/blob/d2af5900fce38238d1202aa43e7332b20add6205/bin/tmux-session) allows you to save a session simply with <code class="language-bash">tmux-session save</code>, and after a reboot, simply use <code class="language-bash">tmux-session restore</code>. It saves it to <code class="language-bash">~/.tmux-session</code>.