---
layout: post
title: "Changing WSL Hostname"
date: 2024-09-15
categories: [wsl, linux]
---

https://www.reddit.com/r/bashonubuntuonwindows/comments/q7mox0/is_changing_the_hostname_on_wsl_still_discouraged/?captcha=1

I have followed the instructions [here](https://www.srccodes.com/change-hostname-ubuntu-microsoft-windows-subsystem-for-linux-wsl-wsl2-wsl-conf-unable-resolve-hosts-name-service-not-known-list-running-shutdown-vm-srccodes/) to change the hostname of the Ubuntu instance and it seems to work okay.

Basically:

1. Add following lines in /etc/wsl.conf.

 [network]  
 hostname = <new name>  
 generateHosts = false

2. Update /etc/hosts, replace all old occurrences of <old name> with <new name>

3. Restart Ubuntu
