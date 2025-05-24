
---
layout: post
title: "Dropbox Setup on Linux"
date: 2024-09-15
categories: [dropbox, linux]
---

sudo mount -o loop /mnt/e/Dropbox_Guido/virtual_ext_device.img /home/guido/Dropbox

cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -

screen ./.dropbox-dist/dropboxd 

python dropbox.py status

