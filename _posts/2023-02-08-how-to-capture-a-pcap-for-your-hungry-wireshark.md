---
layout: post
title:  "How to Capture PCAPs"
date:   2023-02-08
author:
  - "Mario Raciti"
tags: dfir
---

TODO:
<!-- readmore -->

![cover]()

** ―

## Table of Contents

- [Linux](#linux)
- [Mac](#mac)
- [Windows](#windows)

## Linux

The best tool to capture network traffic is **tcpdump**. This is included with several Linux distributions, so chances are, you already have it installed.

To capture network packets in *any* interface and store the results in a `trace.pcap` file, just open your favourite Terminal and type as follows:

```sh
sudo tcpdump -i any -w trace.pcap
```

Note that if you want to capture the packets in a specific interface, you can find all the available interfaces by executing the following command:

```sh
sudo tcpdump -D
```

## Mac

Similarly to Linux distros, MacOS has **tcpdump** as built-in packet trace tool.

To capture network packets in *any* interface and store the results in a `trace.pcap` file, just open your favourite Terminal and type as follows:

```sh
sudo tcpdump -i any -w trace.pcap
```

Note that if you want to capture the packets in a specific interface, you can find all the available interfaces by executing the following command:

```sh
sudo tcpdump -D
```

Alternatively, you can also determine the correct interface name by running the `networksetup` command-line tool with the `-listallhardwareports` argument. This prints a list of network interfaces also including a user-visible name.

```sh
$ networksetup -listallhardwareports


Hardware Port: Ethernet
Device: en0
Ethernet Address: 54:45:5b:01:ca:89


Hardware Port: Wi-Fi
Device: en1
Ethernet Address: 78:a1:3c:02:2b:da


… and so on …
```

## Windows

[QuickPcap.ps1](https://github.com/dwmetz/QuickPcap/blob/main/QuickPcap.ps1) is a quick and easy PowerShell script to collect a packet trace in a `.etl` format. Note that you need to download the latest [etl2pcapng](https://github.com/microsoft/etl2pcapng/releases) to convert the trace to a `.pcap` format.

*Further information can be found in [this article](https://bakerstreetforensics.com/2022/01/07/quickpcap-capturing-a-pcap-with-powershell/), where the author explains all the details.*

```ps1
<#
QuickPcap.ps1
https://github.com/dwmetz
Author: @dwmetz
Function: This script will use the native functions on a Windows host to collect a packet capture as an .etl file.
Note the secondary phase where etl2pcapng is required to convert to pcap.
#>

# Get the local IPv4 address
$env:HostIP = (
    Get-NetIPConfiguration |
    Where-Object {
        $_.IPv4DefaultGateway -ne $null -and
        $_.NetAdapter.Status -ne "Disconnected"
    }
).IPv4Address.IPAddress

# Capture until the specified Sleep duration (default: 90s)
netsh trace start capture=yes IPv4.Address=$env:HostIP tracefile=c:\temp\capture.etl
Start-Sleep 90
netsh trace stop

# Convert .etl to .pcap
Set-Location C:\Tools\etl2pcapng\x64 
./etl2pcapng.exe c:\temp\capture.etl c:\temp\capture.pcap
Set-Location C:\Temp
Get-ChildItem
```

To execute QuickPcap.ps1 just open a PowerShell and type as follows:

```ps1
File_Location\QuickPcap.ps1
```

---

### References

- <https://opensource.com/article/18/10/introduction-tcpdump>
- <https://developer.apple.com/documentation/network/recording_a_packet_trace>
- <https://bakerstreetforensics.com/2022/01/07/quickpcap-capturing-a-pcap-with-powershell/>
