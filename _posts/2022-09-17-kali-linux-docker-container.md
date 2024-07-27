---
layout: post
title:  "Kali Linux on a Docker Container: the easiest way"
date:   2022-09-17
author:
  - "Mario Raciti"
tags: docker offensive
---

A simple dockerfile which allows you to build a docker image starting from the latest official one of Kali Linux and including some useful tools.

![cover](https://www.kali.org/blog/official-kali-linux-docker-images/images/kali-linux-docker-images.jpg)
<!-- readmore -->

*Awareness without action is worthless*. - Phil McGraw

You do need **Kali Linux** for work purposes, but you think that creating and using a *virtual machine* might be **dispendious** for your physical host. So, if you don’t need the *Kali GUI*, here is a **solution** to performance problems and waste of time: a **Docker container**. In this article, I will show you a fast way to get a Kali container. Besides, you will also learn how to build a *self-updated* image of Kali, which includes some *useful* tools*, to create a **customised container**.

---

### Before proceeding

This is not a tutorial on how to install **Docker**, since you can follow the official docs at <https://docs.docker.com/install/>. Whether you are a *Windows* or *MacOS* user, I suggest you to download and install the **Docker Desktop** version that you can find, each in order, at <https://docs.docker.com/docker-for-windows/install/> and <https://docs.docker.com/docker-for-mac/install/>.

---

## Kali Linux from the Official Repo

The fastest way to create a **Docker container** is to pull an *image* from the [Docker Hub](https://hub.docker.com/), a service provided by Docker for finding and sharing container images. So, open your favourite **Terminal** and pull the [official Kali Linux Docker image](https://hub.docker.com/r/kalilinux/kali-rolling), typing as follows:

```sh
docker pull kalilinux/kali-rolling
```

Once this process is over, let’s create a *Kali container* by typing:

```sh
docker run -ti kalilinux/kali-rolling /bin/bash
```

and **we are done**! Now you have your **Kali Linux container** perfectly *working*.

## Kali Linux from a Dockerfile

The more elegant and recommended way to build a Docker image is to write a **Dockerfile**. So I wrote a Dockerfile - you can find it at <https://github.com/tsumarios/Kali-Linux-Dockerfile> - that you could *modify* by adding or removing tools, commands and configuration files, as you prefer. This Dockerfile gets the [latest official Kali Linux Docker image](https://hub.docker.com/r/kalilinux/kali-rolling) and, after doing some updates, installs some tools such as **zsh shell**, which is my favourite one, and the [Kali Linux Top 10 metapackage](https://hub.docker.com/r/kalilinux/kali-rolling). Other included tools are: *exploitdb, man-db, dirb, nikto, wpscan, uniscan, tor, proxychains*.

### Build Kali Image

To build an *image* from this dockerfile, just go into the folder where it is located and simply open your favourite **Terminal**, typing as follows:

```sh
docker build [-t your_image_name] .
```

Note that with the -t parameter you can specify a customised name for the image, which you’ll specify in the next command.

### Create Kali Container

At this point, you have to create a new *container* from the just-built image, by typing:

```sh
docker run -ti <your_image_name>
```

Note that, unlikely the case of the *standard official image*, you don't need to specify the *entry point*. This is due to the last row in our Dockerfile which sets the *entry point* to the *zsh shell*.

Now you have your **fully customised** *Kali Linux container* running!

---

## Conclusions

Docker is a powerful tool which *simplifies* lots of processes. Indeed, as we saw, you can easily create a *ready-to-use* **Kali Linux container** in a few minutes. Moreover, a Dockerfile makes configurations *easier, fully customisable* - including your favourite and useful tools - and *repeatable*.

---

### References

- <https://github.com/tsumarios/Kali-Linux-Dockerfile>
- <https://hub.docker.com/r/kalilinux/kali-rolling>
