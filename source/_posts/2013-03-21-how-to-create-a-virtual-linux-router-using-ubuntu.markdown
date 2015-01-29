---
layout: post
title:  "How to Create a Virtual Linux Router"
date:   2013-03-21
categories: linux
---
##How to Create a Virtual Ubuntu Linux Router

In order to use our own DHCP server in VMware Workstation/Fusion (and even Virtual Box), we need to use the Host-Only Networking feature.  The reason we need a totally private virtual network is that Bridged Networking would put us on the production network and that's not an option.  Network Address Translation networking would give us a private network, but it's impossible to turn off the dhcp server built into VMware Workstation/Fusion and Virtual Box.  This guide is the setup of a virtual ubuntu linux machine that will have two virtual network interfaces and be configured as a NAT router.  The first network interface will be configured in VMware to NAT and the second network interface will be configure in VMware to Host-Only, acting as our private network's gateway.

Ok, so technically we don't need internet access to perform our [[Capstone Tasks]], but whatever.

##Create the Virtual Router Image
Now that you've already created your [[How to make an Ubuntu Gold Master Image|ubuntu server gold master]], clone it and call it "router".

Now before you power it up, let's change some settings.

Open up the virtual machine setting control panel.

##Network Adapter Configuration

There should already be a network interface adapter connected to the virtual machine.  Make sure it's of type NAT.

Add a second interface and make sure it's of type "Host Only".  Fusion, on my mac only allows for one private network, VMWare Workstation allows for up to 7 different private networks.  If you're on VMware Workstation, make a note of witch one you chose because all of the rest of our virtual machine will have to be configured on the same private network in order to communicate with each other.

##Processors & RAM
Because we're going to be running so many virtual machines on one host, let's change the memory of the linux router to be only what is needed to perform it's duty.

Click on the Processors & RAM settings and change it to be 128 MB of RAM.

The Processor should already be configured to be 1 CPU, leave it there.

##USB Devices
Disable USB Devices, just uncheck "Enable USB 2.0 support".

We're done now power up the virtual machine.

##Change the Hostname 
Bash 
Login as the user you created the gold master as and then fire up a root shell.
  sudo -i
Sudo, as you know, allows you to run commands as root, the "dash i" creates a root shell by simulating an initial login.  This isn't typically a good idea, but I'm awesome and I know what I'm doing.

Let's change the hostname.
{% highlight bash %}
vi /etc/hostname
{% endhighlight %}
Change it to "router" and save it.

Now edit the hosts file.
{% highlight bash %}
vi /etc/hosts
{% endhighlight %}

You'll see two entries for the loopback address (127.0.0.1), change the second one to "router".

reboot and login, the hostname is now changed.

##Install IP Tables
{% highlight bash %}
apt-get install iptables
{% endhighlight %}

##Configure the Network Interfaces
The first network interface should be already working and configured for dhcp so let's add the configuration for the second interface.
{% highlight bash %}
vi /etc/network/interfaces
{% endhighlight %}

{% highlight bash %}
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet dhcp
  pre-up iptables-restore < /etc/iptables.rules

# The inside interface
auto eth1
iface eth1 inet static
  address 10.10.10.1
  netmask 255.255.255.0
  network 10.10.10.0
  broadcast 10.10.10.255
{% endhighlight %}

##Enable IPv4 Forwarding
This will make ip forwarding permanent
{% highlight bash %}
vi /etc/sysctl.conf
{% endhighlight %}

Find this line and make it look the same:
{% highlight bash %}
# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1
{% endhighlight %}

##Add the IP Table rules and save them
{% highlight bash %}
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
{% endhighlight %}

{% highlight bash %}
iptables-save > /etc/iptables.rules
{% endhighlight %}
  
##Rules for it20
{% highlight bash %}
iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
iptables -A FORWARD -i eth1 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i eth0 -o eth1 -j ACCEPT
{% endhighlight %}

{% highlight bash %}
root@it20:~# iptables -L -n -v 
Chain INPUT (policy ACCEPT 2035K packets, 2873M bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
 2176  654K ACCEPT     all  --  eth1   eth0    0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
 2594  537K ACCEPT     all  --  eth0   eth1    0.0.0.0/0            0.0.0.0/0           

Chain OUTPUT (policy ACCEPT 1105K packets, 61M bytes)
 pkts bytes target     prot opt in     out     source               destination         
root@it20:~# 
{% endhighlight %}

Problems? try flushing:
{% highlight bash %}
iptables -F
iptables -F -t nat
{% endhighlight %}

##On startup
The script /etc/network/if-pre-up.d/iptablesload will contain:
{% highlight bash %}
#!/bin/sh
iptables-restore < /etc/iptables.rules
exit 0
{% endhighlight %}