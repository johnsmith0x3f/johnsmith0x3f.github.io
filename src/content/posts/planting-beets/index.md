---
title: Planting Beets
published: 2025-06-27
description: "Plant beets in pods."
image: ""
tags: [ "beets", "docker", "podman" ]
category: ""
draft: false
lang: ""
---

> Hi. I'm Saul Podman. Did you know that you have a bad music collection? The music geek's media organizer says you do, and so do I. I believe that, until managed by an automated process, every single, album, and compilation in your library is unorganized. And that's why I fight for you, audiophiles!
> <p align="right">-- Saul Podman</p>

But why Podman? Why not Docker? The main reason is that **Podman is rootless by default**. To use Docker, you should either have root privilage, or be in the `docker` user group, which also have root privilage at runtime. So when you use Docker, there is a chance of messing up your system, although very small. Podman, on the other hand, can be run directly by a non-root user.

## Install Podman

Follow [the official instructions](https://podman.io/docs/installation) to install Podman on your system.

For those who are not familiar with Docker nor Podman, you might want to read [what is a container](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container) and [what is an image](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image) first, or:

:::tip[TL;DR]
- An image is a static (until manually rebuilt) virtual environment built before runtime.
- A container is a said runtime, which is dynamic, and usually dropped on exit.

Suppose we need to use `bash` in our project. We would install `bash` when building our image, so that every container based on this image will have `bash` available. Whenever we create a new container and run `bash`, a new `.bashrc` file will be created, and when we remove the container, this file will also be cleaned.
:::

## Configure Image

```dockerfile
FROM python:3.13-alpine
RUN apk add --no-cache fish

COPY requirements.txt /tmp
RUN pip install --no-cache-dir -r /tmp/requirements.txt

WORKDIR /root/Music
```

Here, we use [fish](https://fishshell.com) as our shell in the container, because it has syntax highlighting, auto suggestion and tab completions by default. The `requirements.txt` conatains only one package `beets` for now. We will add more when needed.

Now we run
```sh
podman build -t beetpod .
```
to build the image. This tells `podman` to `build` an image tagged `beetpod` using the `Containerfile` in current directory.
```sh
podman run --init --rm -it \
    -v "$HOME/Music:/root/Music" \
    -v "$XDG_CONFIG_HOME/beets:/root/.config/beets" \
    beetpod fish
```

## Make a Makefile

We can make a Makefile so that we can perform our tasks with `make build` and `make run`.
```make
HOME := $(shell echo $$HOME)
XDG_CONFIG_HOME := $(shell echo $$XDG_CONFIG_HOME)

build:
	@podman build -t beetpod .

run:
	@podman run --init --rm -it \
		-v "$(HOME)/Music:/root/Music" \
		-v "$(XDG_CONFIG_HOME)/beets:/root/.config/beets" \
		beetpod fish
```
