---
layout: post
title:  "How to Set Up WireGuard without Getting Headaches"
date:   2024-07-28
author:
  - "Mario Raciti"
tags: hardening android web network
cover_image: "https://img.freepik.com/free-photo/3d-rendering-blockchain-technology_23-2151480202.jpg?t=st=1722100729~exp=1722104329~hmac=013bb1e6c591e6b6cd6138aef3df1dffad6b19266ad63dde59a7c9d0bc2cd0b2&w=1800"
---

This blog post provides a clear, step-by-step guide to setting up WireGuard on a Raspberry Pi running Red Hat-Compatible OSs and configuring an Android device to connect to it ― without headaches!
<!-- readmore -->

![cover](https://img.freepik.com/free-photo/3d-rendering-blockchain-technology_23-2151480202.jpg?t=st=1722100729~exp=1722104329~hmac=013bb1e6c591e6b6cd6138aef3df1dffad6b19266ad63dde59a7c9d0bc2cd0b2&w=1800)

*It’s not that I’m so smart, it’s just that I stay with problems longer.* ― Albert Einstein

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Raspberry Pi Configuration](#raspberry-pi-configuration)
- [Android Configuration](#android-configuration)
- [Check Connection](#check-connection)
- [Conclusions](#conclusions)

## Introduction

[WireGuard](https://www.wireguard.com/) is a modern, high-performance VPN solution that is simple to configure and use. This guide will walk you through setting up WireGuard on a Raspberry Pi running [Red Hat-compatible OSs](https://en.wikipedia.org/wiki/Red_Hat_Enterprise_Linux_derivatives) (i.e., [Alma Linux](https://almalinux.org/), [Rocky Linux](https://rockylinux.org/), [Fedora](https://fedoraproject.org/), [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux), etc.) and configuring an Android device to connect to it.

*Note that this setup was tested on Rocky Linux 9. Repositories and package names may change for other Red Hat-compatible distros.*

## Prerequisites

1. **Raspberry Pi with Red Hat-compatible OS**: Ensure your Raspberry Pi is running a Red Hat-compatible OS (Rocky Linux in our case) and is updated.
2. **Android Device**: Ensure your Android device has the WireGuard app installed from the Google Play Store.
3. **Basic Linux Knowledge**: Familiarity with terminal commands and text editing in Linux.

## Raspberry Pi Configuration

### Step 1: Install WireGuard on Raspberry Pi

First, we need to install WireGuard on the Raspberry Pi.

Let's add the WireGuard repository:

```sh
sudo dnf install epel-release
sudo dnf config-manager --set-enabled powertools
```

And install WireGuard:

```sh
sudo dnf install wireguard-tools
```

### Step 2: Configure WireGuard on Raspberry Pi

#### Generate Keys

Generate the private and public keys for the WireGuard interface:

```sh
wg genkey | tee /etc/wireguard/privatekey | wg pubkey | tee /etc/wireguard/publickey
```

####  Create the WireGuard Configuration File

Create the configuration file for the WireGuard interface:

```sh
sudo vi /etc/wireguard/wg0.conf
```

And add the following content to the file, replacing `<private_key>` with the private key of the Raspberry Pi (`/etc/wireguard/privatekey`) and `<public_key>` with the peer's public key (the Android device in our case).

```ini
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820  # Default port
PrivateKey = <private_key>
# Firewall Settings
PostUp = firewall-cmd --zone=public --add-masquerade
PostUp = firewall-cmd --direct --add-rule ipv4 filter FORWARD 0 -i wg0 -o eth0 -j ACCEPT
PostUp = firewall-cmd --direct --add-rule ipv4 nat POSTROUTING 0 -o eth0 -j MASQUERADE
PostDown = firewall-cmd --zone=public --remove-masquerade
PostDown = firewall-cmd --direct --remove-rule ipv4 filter FORWARD 0 -i wg0 -o eth0 -j ACCEPT
PostDown = firewall-cmd --direct --remove-rule ipv4 nat POSTROUTING 0 -o eth0 -j MASQUERADE

# Android Device
[Peer]
PublicKey = <public_key>
AllowedIPs = 10.0.0.2/32    # Unique IP address of the peer

# Other peers can be added similarly, ensuring each has a different IP address
# [Peer]
# PublicKey = <peer_public_key>
# AllowedIPs = 10.0.0.3/32
```

#### Set SELinux to Permissive Mode and Adjust Firewall Rules

Ensure that SELinux is running on `permissive` mode:

```sh
vi /etc/selinux/config
```

Configure the SELINUX=permissive option:

```ini
# This file controls the state of SELinux on the system

# SELINUX= can take one of these three values
# enforcing - SELinux security policy is enforced
# permissive - SELinux prints warnings instead of enforcing
# disabled - No SELinux policy is loaded
SELINUX=permissive

# SELINUXTYPE= can take one of these two values
# targeted - Targeted processes are protected
# mls - Multi Level Security protection
SELINUXTYPE=targeted
```

Then, ensure that the firewall rules are correctly set to allow traffic through the WireGuard interface:

```sh
sudo firewall-cmd --zone=public --add-port=51820/udp --permanent
sudo firewall-cmd --reload
```

### Step 3: Enable and Start WireGuard

Finally, Enable and start the WireGuard service on the Raspberry Pi:

```sh
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0
```

---

## Android Configuration

The configuration of the Android device is quite straightforwarding.

1. *Install the WireGuard App*: Download and install the WireGuard app from the [Google Play Store](https://play.google.com/store/apps/details?id=com.wireguard.android&hl=en).

2. *Create a New Tunnel*: Open the WireGuard app and tap the `+` button to add a new tunnel.

3. *Generate Keys*: The app will automatically generate keys for you.

4. *Configure the Tunnel*: Enter the following configuration:

    ```ini
    [Interface]
    PrivateKey = <android_private_key>
    Address = 10.0.0.2/32   # Choose a unique IP address. This must be different for each peer
    DNS= 1.1.1.1, 1.0.0.1   # Optional DNS (Cloudflare)

    [Peer]
    PublicKey = <raspberry_public_key>
    AllowedIPs = 0.0.0.0/0, ::/0
    Endpoint = raspberry.public.ip.address:51820    # In case of dynamic IP, you can set DDNS and specify the domain instead of the address. Same port as Raspberry Pi (default: 51820)
    PersistentKeepalive = 25 # Optional
    ```

5. *Save and Activate*: Save the configuration and activate the tunnel.

## Check Connection

After configuring both the Raspberry Pi and the Android device, verify the connection by checking the status on both ends. On the Raspberry Pi, you can verify the connection by running `sudo wg show`, while on Android you can simply open the WireGuard app and ensure the tunnel is connected.

## Conclusions

As a result of the steps described in this blog post, we finally have a secure WireGuard VPN set up between our Raspberry Pi running a Red Hat-compatible OS and our Android device. This setup provides a secure and private connection to our home network, enhancing online privacy and security.

---

### References

- <https://www.wireguard.com/>
