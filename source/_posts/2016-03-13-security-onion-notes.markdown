---
layout: post
title: "Security Onion Notes"
date: 2016-03-13 10:54:42 -0400
comments: true
categories: 
---
Keep console from blanking
edit /etc/rc.local
Add 
setterm -blank 0 
above 
exit 0 


Stop X from starting up
Edit /etc/default/grub with your favorite editor, eg: nano:

sudo nano /etc/default/grub

Find out this line:

GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"

Change it to:

GRUB_CMDLINE_LINUX_DEFAULT="text"

Update Grub:

sudo update-grub

you can start X by typing startx


Performance:
mount /nsm on separate disk