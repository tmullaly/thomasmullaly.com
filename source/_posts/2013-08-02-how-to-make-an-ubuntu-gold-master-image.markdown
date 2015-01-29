---
layout: post
title:  "How to Make an Ubuntu Gold Master"
date:   2013-08-02
categories: linux
---
This guide show you how to make a gold master virtual machine image of Ubuntu 12.04 LTS.  The purpose of this is because we'll be creating a lot of new virtual machines and it's far easier to clone a master image than to install each and every time.  Let's get started...
<!-- more -->
#Download Ubuntu Server 12.04 64 bit
http://www.ubuntu.com/download/server

Now go into VMWare and create the master virtual machine by select new virtual machine.

#Go to VMware and create a new VM

Choose typical

Choose the ubuntu installer disk and select "I will install the operating system later"

Choose Linux Version Ubuntu

Call this virtual machine Ubuntu

Make the Disk Size 10 GB

Add the Ubuntu iso to the virtual cd

Power on the Virtual machine


Press Enter to choose the English language

Then, Press F4 to install a minimal virtual machine

Then press enter twice

This will kick off the install, choose all defaults

<del>only install the OpenSSH service</del>
Don't install anything!!! Puppet will take care of everything.

#Update and Upgrade
login when done and run:
  sudo apt-get update
  sudo apt-get upgrade
  sudo apt-get dist-upgrade

#Add Virtual Machine Tools
If you are on Virtual Box:
  sudo apt-get install dkms

If you are on VMWare:
  sudo apt-get install open-vm-tools

#Add the puppet client agent
  sudo apt-get install puppet
  
Configure the puppet defaults
  vi /etc/defaults/puppet
  
# Defaults for puppet - sourced by /etc/init.d/puppet

# Start puppet on boot?
START=yes

# Startup options
DAEMON_OPTS="--report"


Puppet should start on boot and look to the master for the manifests to define it.

#eth0 eth1 problem when cloning
The root of the problem at our site was udev's handling of devices. Udev keeps a history of devices attached to the computer. What makes sense for i.e. USB sticks (that is, each USB stick you attach will be recorded and mounted to the same point when attached again at a later point in time), causes problems when systems are cloned.
 
There is a file /etc/udev/rules.d/z25_persistent-net.rules, where udev memorises, which hardware device should get named eth0. Here it looks like this:
{% highlight bash %}
  \# PCI device 14xx:16xx (tg3)
  ACTION=="add", SUBSYSTEM=="net", DRIVERS=="?*", SYSFS\{address}=="00:11:xx:xx:xx:xx", NAME="eth0"
{% endhighlight %}
When you run the image on another computer, the new network adapter is recognised on the PCI bus and a new entry in the above mentioned file is generated. "eth0" is already given to device 14xx:16xx, so the new device gets named "eth1".
 
If you delete this file, it will be rebuilt during the next system startup and your problem should go away.
  
#Shutdown
now shutdown, you now have your golden master


shutdown the VM, this is the golden master