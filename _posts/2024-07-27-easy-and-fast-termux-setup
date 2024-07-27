---
layout: post
title:  "Easy and Fast Termux Setup"
date:   2024-07-27
author:
  - "Mario Raciti"
tags: android resources
---

A Termux setup with essential tools, configuring zsh, and enhancing Neovim with plugins.

![cover](https://img.freepik.com/free-photo/view-cartoon-animated-3d-penguin_23-2150882064.jpg?t=st=1722085076~exp=1722088676~hmac=64168ac7035e3186d87a371a614fcdad1584427a6262bd8e048605ecaf6b2840&w=1800)

*"The only limit to our realisation of tomorrow is our doubts of today."* ― Franklin D. Roosevelt

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Customising the Shell with zsh](#customising-the-shell-with-zsh)
- [Configuring Neovim](#configuring-neovim)
- [Conclusions](#conclusions)

## Introduction

Termux is an Android terminal emulator and Linux environment application that works directly with no rooting or setup required. It provides a powerful base for installing and managing packages.

## Prerequisites

Before we begin, ensure having Termux installed on our Android device. It can be downloaded from the [Google Play Store](https://play.google.com/store/apps/details?id=com.termux). However, the recommended way to install Termux is from the [F-Droid repository](https://f-droid.org/en/packages/com.termux/) or the [Github repository](https://github.com/termux/termux-app).

## Setup

First, let's update the package lists and upgrade any existing packages:

```sh
pkg update && pkg upgrade
```

Next, let's install the some useful packages:

```sh
pkg install curl git python3 dnsutils neovim openssh openssl wget zip unzip nodejs ruby golang grep libxml2-utils zsh figlet htop bat ffmpeg php rust fastfetch perl sqlite
```

## Customising the Shell with zsh

[zsh](https://ohmyz.sh/) is a powerful shell that can replace the default Termux shell for an enhanced command-line experience. To set up zsh:

1. Clone the zsh setup repository and run the setup script:

    ```sh
    git clone https://github.com/Sohil876/Termux-zsh.git && cd Termux-zsh
    bash setup.sh
    ```

2. After the script completes, let's restart Termux to start using zsh.

## Configuring Neovim

[Neovim](https://neovim.io/) is a highly configurable text editor, built for effective coding. To set up Neovim with plugins:

1. Download the Neovim and Vim Plugins configuration files:

    ```sh
    curl -fLo ~/.config/nvim/init.vim --create-dirs https://raw.githubusercontent.com/hootan09/vim-termux/main/init.vim

    curl -fLo ~/.config/nvim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/hootan09/vim-termux/main/plug.vim
    ```

2. Open Neovim and run:

    ```sh
    :PlugInstall
    ```

    This will download and install the plugins specified in the `init.vim` file.

## Conclusions

With Termux, our Android device can become a powerful, portable development environment. By following this quick setup, we have installed essential tools, customised our shell with zsh, and configured Neovim with useful plugins.

---

### References

- <https://github.com/Sohil876/Termux-zsh>
- <https://github.com/hootan09/vim-termux>
