---
title: Some tmux Tricks
published: 2025-08-02
description: "Some tmux tricks I use."
image: ""
tags: [ "terminal", "tmux" ]
category: ""
draft: false
lang: "en"
---

## Alternative Prefix

There is a well-known trick to re-map `<Caps>` to `<Esc>` when tapped and `<Ctrl>` when held. This trick saves us from lifting our hands to reach the `<Esc>` or `<Ctrl>` key. But it do bring up another problem: `<Caps-a>` is too hard to press. To resolve this, we may further map `<Caps-;>` to `<Ctrl-a>`. This is a smart solution, because it can make use of a invalid key-combo and is very easy to press.

I use `keyd` do all these re-mapping. Below is my config:

```conf title="/etc/keyd/keyd.conf"
[ids]

*

[main]
# Make <Caps> work as <Esc> when tapped and <Ctrl> when held.
capslock = overload(capslock, esc)

[capslock:C]
# Map <Caps-;> behave like <Ctrl-a> (for GNU screen and tmux).
; = C-a
```

Then in my `tmux` config, I have:

```tmux title="$XDG_CONFIG_HOME/tmux/tmux.conf"
unbind-key C-b
set-option -g prefix C-a
bind-key C-a send-prefix
```

which changes the prefix to `<Ctrl-a>` and can be triggered by `<Caps-;>`.
