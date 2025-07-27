---
title: Planting Beets
published: 2025-06-27
description: "Plant beets in pods."
image: ""
tags: [ "beets", "docker", "podman" ]
category: ""
draft: true
lang: ""
---

> Hi. I'm Saul Podman. Did you know that you have a bad music collection? The music geek's media organizer says you do, and so do I. I believe that, until managed by an automated process, every singleton, album, and compilation in your library is unorganized. And that's why I fight for you, audiophiles!
> <p align="right">-- Saul Podman</p>

<!-- I have been scraping and hoarding music for about a year now. Although manually organizing every file is what people call "the best way to really get to know your music", it is still a very time-consuming process. Luckily, I came across beets, a CLI program that can automatically fetch and edit the names and metadatas of music I collected. -->
<!---->
<!-- The thing is, beets is imperfect. It does many things that I do not intend. But what is really great is that it provides integration with my custom scripts and plugins. I do like beets, but I do not want to mess up my. To manage it with ease, I decided to put it in a pot. -->
<!---->
<!-- But why Podman? Why not Docker? The main reason is that **Podman is rootless by default**. To use Docker, you should either have root privilage, or be in the `docker` user group, which also have root privilage at runtime. So when you use Docker, there is a chance of messing up your system, although very small. Podman, on the other hand, can be run directly by a non-root user. -->

## Install Podman

Follow [the official instructions](https://podman.io/docs/installation) to install Podman on your system.

For those who are not familiar with Docker nor Podman, you might want to read [this](https://developers.redhat.com/blog/2018/02/22/container-terminology-practical-introduction) first, or:

:::tip[TL;DR (AFAIK)]
- An image is a static (until manually rebuilt) virtual environment built before runtime.
- A container is a said runtime, which is dynamic, and usually dropped on exit.

Suppose we need to use `bash` in our project. We would install `bash` when building our image, so that every container based on this image will have `bash` available. Whenever we create a new container and run `bash`, a new `.bashrc` file will be created, and when we remove the container, this file will also be cleaned.
:::

## Build Image

We will write a `Containerfile` (which is essentially a [Dockerfile](https://docs.docker.com/get-started/docker-concepts/building-images/writing-a-dockerfile)) to tell Podman how we want our image to be built.

```dockerfile title="Containerfile"
FROM python:3.13-alpine
RUN apk add --no-cache fish

COPY requirements.txt /tmp
RUN pip install --no-cache-dir -r /tmp/requirements.txt

WORKDIR /root/Music
```

Here, we use [fish](https://fishshell.com) as our shell in the container, because it has syntax highlighting, auto suggestion and tab completions by default. The `requirements.txt` contains only one package `beets` for now. We will add more when needed.

Then we run
```sh
podman build -t beetpod .
```
to build the image. This tells Podman to build an image tagged `beetpod` using the `Containerfile` in current directory.

## Run Container

Run
```sh
podman run --rm -it \
    -v "$HOME/Music:/root/Music" \
    -v "$XDG_CONFIG_HOME/beets:/root/.config/beets" \
    beetpod fish
```
This will run `fish` in a new container from the `beetpod` image. It will mount the locations on your local machine to the corresponding locations in the container, start `fish` in interactive mode in a pseudo-TTY, and auto-remove the container on exit.

:::warning
If `$XDG_CONFIG_DIR` is not set on your system, you should use `$HOME/.config/beets` instead.
:::

## Make a Makefile

We can make a `Makefile` to simplify our commands.
```make
HOME := $(shell echo $$HOME)
XDG_CONFIG_HOME := $(shell echo $$XDG_CONFIG_HOME)

build:
	@podman build -t beetpod .

run:
	@podman run --rm -it \
		-v "$(HOME)/Music:/root/Music" \
		-v "$(XDG_CONFIG_HOME)/beets:/root/.config/beets" \
		beetpod fish
```
Now we can build the image and run the container with `make build` and `make run` respectively.
