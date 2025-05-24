---
layout: post
title: "Keeping WSL2 Alive in Background"
date: 2024-09-15
categories: [wsl, windows]
---

```
[wsl2]
vmIdleTimeout=-1
```

in %userprofile%/.wslconfig

I actually now found a command that starts WSL 2 VM, exits, and WSL 2 VM and instance are left running:

This next command keeps it running in the background
```
wsl --exec dbus-launch true && wsl
```

May need to run docker on windows to keep alive


guido@farty:~$ sudo pro attach C13CjT3FEPjYJp8iAVnY3oWh9gUvED
Enabling Ubuntu Pro: ESM Apps
Ubuntu Pro: ESM Apps enabled
Enabling Ubuntu Pro: ESM Infra
Ubuntu Pro: ESM Infra enabled
This machine is now attached to 'Ubuntu Pro - free personal subscription'

SERVICE          ENTITLED  STATUS       DESCRIPTION
anbox-cloud      yes       disabled     Scalable Android in the cloud
esm-apps         yes       enabled      Expanded Security Maintenance for Applications
esm-infra        yes       enabled      Expanded Security Maintenance for Infrastructure
landscape        yes       disabled     Management and administration tool for Ubuntu

NOTICES
Operation in progress: pro attach

For a list of all Ubuntu Pro services, run 'pro status --all'
Enable services with: pro enable <service>

     Account: 2guido@gmail.com
Subscription: Ubuntu Pro - free personal subscription

syslog complains about /mnt/c/user/user/.ubuntupro/.address
I unistalled 
guido@farty:~$ sudo apt remove wsl-pro-service
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  wsl-pro-service
0 upgraded, 0 newly installed, 1 to remove and 102 not upgraded.
After this operation, 12.9 MB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 43942 files and directories currently installed.)
Removing wsl-pro-service (0.1.4) ...


This below seems to work.
Run this vbs script on windows startup. Just put it in the windows startup folder (crl+r -> `shell:startup`)

wsl-startup.vbs:

```# change '<Distro>' to the distro name you are using.
set ws = CreateObject("WScript.Shell")
ws.Run "wsl -d Ubuntu-24.04", 0, True
```
or put it here:
brita ralph@ARTY C:\Users\Brita Ralph\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>more wsl-startup.vbs
set ws = CreateObject("WScript.Shell")
ws.Run "wsl -d Ubuntu-24.04", 0, True


or this from another user:
```
```
Set objShell = WScript.CreateObject("WScript.Shell")
objShell.Run "wsl.exe --distribution Ubuntu-24.04 --exec dbus-launch true", 0, True
objShell.Run "wsl.exe --distribution Ubuntu-24.04", 1, True
```

I also tried this as a startup file, but I don't know if works as well:
@echo off
echo "Starting Ubuntu..."
wsl -d Ubuntu-24.04 -u root service dbus start

REM Wait a bit to ensure WSL starts properly
timeout /t 5

echo "Mounting drive..."
%powershell -Command "Start-Process wsl -ArgumentList '--mount \\\\.\\PhysicalDrive2 --partition 1' -Verb runAs"
wsl --mount \.\\PhysicalDrive2 --partition 1
echo "Done"




