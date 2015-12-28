---
layout: post
title: "Windows 2012R2 Active Directory Remote Shutdown Group Policy"
date: 2015-09-11 21:51:17 -0400
comments: true
categories: ["windows"]
---
This is the bare minimum firewall modifications you need to make to get remote shutdown working on a Windows 2012R2 Active Directory domain working.
<!-- more -->
Open up the Group Policy Management snapin.  Add a new Group Policy Object, call it Remote Administration Firewall Rules.

Edit the Policy:
Computer -> Policies -> Administrative Templates

Network/Network Connections/Windows Firewall/Domain Profile

Windows Firewall: Allow ICMP exceptions: Enabled

Windows Firewall: Allow inbound remote administration exception: Enabled

I would only allow from one ip address, that of a dc, for now anyway.

Link the policy to the OU that contains the computer accounts that you want to apply the policy to.

You should be able to use the shutdown /i command on the allowed server and reboot (or shutdown) the doamin computers. 