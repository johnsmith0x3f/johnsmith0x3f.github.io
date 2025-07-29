---
title: Home Cleanup
published: 2025-07-11
description: "Clean up my home directory."
image: ""
tags: [ "linux" ]
category: ""
draft: false
lang: "en"
---

I have been using Linux (Arch btw) for 15 months now. Over time, my `$HOME` has become cluttered with [dotfiles](https://wiki.archlinux.org/title/Dotfiles), which is a headache for those who tweaks them frequently, like me. After doing some research, I found the [XDG Base Directory](https://wiki.archlinux.org/title/XDG_Base_Directory) specification that can declutter my `$HOME`. So we will start with that.

## XDG Base Directory

Following the specs, we may create a `/etc/profile.d/xdg.sh` file:
```sh
# Set user directories.
export XDG_CACHE_HOME="$HOME/.cache"
export XDG_CONFIG_HOME="$HOME/.config"
export XDG_DATA_HOME="$HOME/.local/share"
export XDG_STATE_HOME="$HOME/.local/state"

# Set system directories.
export XDG_CONFIG_DIRS="/etc/xdg"
export XDG_DATA_HOME="/usr/local/share:/usr/share"
```
When we login with `bash` or other bash-style shells, it will source `/etc/profile`, which will then source all the files in `/etc/profile.d`, including this one, which will set the needed environment variables.

:::note

For non-bash-style shells, please refer to their own login script conventions.

:::

## Using Custom Locations

But wait, if we just use the suggested default values, what is the point of setting the values ourselves? So, in order to align with the hierarchy in `/` (system root), we may wrote it this way instead:
```sh
# Set user directories.
export XDG_CACHE_HOME="$HOME/.var/cache"
export XDG_CONFIG_HOME="$HOME/.etc"
export XDG_DATA_HOME="$HOME/.usr/share"
export XDG_STATE_HOME="$HOME/.var/lib"

# Set system directories.
export XDG_CONFIG_DIRS="/etc/xdg"
export XDG_DATA_HOME="/usr/local/share:/usr/share"
```
However, after I changed the values, cleaned the old files and rebooted the system, the `~/.config` directory is created again. After some further research, I learned that it is because some programs are started before login, and hence will not be affected by the values we set, but fallback to default ones.

To resolve this, we need the help of [PAM](https://en.wikipedia.org/wiki/Linux_PAM). First add a line in `/etc/pam.d/system-login`:
```pamconf
session required pam_env.so user_readenv=1 user_envfile=.etc/pam_env.conf
```
Then, we write these in `~/.etc/pam_env.conf` file:
```pamenv
# Set user directories.
XDG_CACHE_HOME          DEFAULT="@{HOME}/.var/cache"
XDG_CONFIG_HOME         DEFAULT="@{HOME}/.etc"
XDG_DATA_HOME           DEFAULT="@{HOME}/.usr/share"
XDG_STATE_HOME          DEFAULT="@{HOME}/.var/lib"

# Set system directories.
XDG_CONFIG_DIRS         DEFAULT="/etc/xdg"
XDG_DATA_HOME           DEFAULT="/usr/local/share:/usr/share"
```

:::tip

I suggest you use tabs to align the columns.

:::

Now we may do the checking steps again, and our changes should apply correctly.
