---
layout: post
title: "Tailscale Setup on WSL"
date: 2024-09-15
categories: [tailscale, networking, wsl]
---

To install
Verify that you are on WSL 2. From Powershell, run the following command: wsl -l -v. In the VERSION column you should see a 2. This means you are using WSL 2.
Start your WSL 2 instance from Powershell by running wsl.exe or opening up a Linux terminal tab (if you have it already configured).
Run the automatic installation script:

curl -fsSL https://tailscale.com/install.sh | sh
More detailed instructions can be found on the Setting up Tailscale on Linux page, including a manual installation process.

After the installation completes, Tailscale will not yet be running, so you will need to follow the text prompt after the installation and run sudo tailscale up. This will post a Tailscale URL that you can select or copy and paste into your browser.
When your browser window launches you will need to authenticate using your SSO identity provider.
Follow the prompts to add this node to your tailnet and you should see your new WSL 2 node in the Machines shortly.
Congratulations! You’ve added your Windows WSL 2 instance to your Tailscale network.

To uninstall
Users can uninstall Tailscale by running the following command: sudo apt-get remove tailscale.
