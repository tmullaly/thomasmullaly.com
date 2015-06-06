---
layout: post
title: "Secure Ubuntu 12.04 Wordpress Server Outline"
date: 2015-04-06 20:58:47 -0400
comments: true
categories: ["security","wordpress","linux","ossec","ubuntu"]
---

1. Operating System
  * Minimal os install
  * Minimal services running
  * Disable IPv6
  * Ossec to watch filesystem integrity, monitor log files and watch for rootkits
  * Script to alert when os/package updates are needed (patching)
  * Firewall (iptables)

2. SSH
  * Harden the sshd config 

3. Apache
  * Harden apache

4. MySql
  * Harden MySQL

5. Postfix
  * Configured as satellite, will only send email to our domain from our relay  

6. Wordpress
  * Install security plugins
    1. iThemes Security
    2. Wordfence
    3. InfiniteWP

7. Backup
  * Backup database (mysql)
  * Backup file system (wordpress directory)
  * Document how to perform full recovery

The process to patch is to apt-get update. apt-get upgrade, then update ossec's database.
This might be automated in the future.
I'm leaving the default ubuntu 12.04 AppArmor policies in place.