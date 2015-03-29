---
layout: post
title: "How To Put A KVM Guest Domain On A Bridged Network"
date: 2015-03-29 15:25:06 -0400
comments: true
categories: ["kvm","virtualization","ubuntu","networking"]
---
I want to put some of the KVM domains that I've been using a bridged network so I can access them on my LAN directly.

I'm using my old mac pro as my KVM server and it has two interfaces.  I'm going to bridge the second interface.  
Install bridge-utils:
<!-- more -->
{% highlight bash %}
apt-get install bridge-utils
{% endhighlight %}

Edit /etc/network/interfaces file:
{% highlight bash %}
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
address 10.0.1.10
netmask 255.255.255.0
gateway 10.0.1.1
dns-nameservers 8.8.8.8 8.8.4.4

#auto eth1
#iface eth1 inet manual

auto br0
iface br0 inet dhcp
bridge_ports eth1
bridge_stp off
bridge_fd 0
bridge_maxwait 0
{% endhighlight %}

Restart networking:
{% highlight bash %}
service networking restart
{% endhighlight %}

tom@kvm:~$ brctl show
{% highlight bash %}
bridge name     bridge id           STP enabled    	interfaces
br0             8000.0017f20dd411    no              eth1
virbr0          8000.000000000000   yes
{% endhighlight %}

create file br0.xml 
{% highlight bash %}
<network>
  <name>br0</name>
  <forward mode="bridge"/>
  <bridge name="br0" />
</network>
{% endhighlight %}

virsh net-list --all
{% highlight bash %}
 Name                 State      Autostart     Persistent
----------------------------------------------------------
 default              active     yes           yes
{% endhighlight %}

Define the bridged interface
{% highlight bash %}
virsh net-define br0.xml
{% endhighlight %}

virsh net-list --all
{% highlight bash %}
 Name                 State      Autostart     Persistent
----------------------------------------------------------
 br0                  active     no            yes
 default              active     yes           yes
{% endhighlight %}

virsh net-start br0

{% highlight bash %}
virsh edit (domain name)
{% endhighlight %}
Change the networking config from:
{% highlight bash %}
<interface type='network'>
  <mac address='52:54:00:a6:99:d9'/>
  <source network='default'/>
  <model type='virtio'/>
  <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
</interface>
{% endhighlight %}
to:
{% highlight bash %}
<interface type='network'>
  <mac address='52:54:00:f9:b7:7f'/>
  <source network='br0'/>
  <model type='virtio'/>
  <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
</interface>
{% endhighlight %}

{% highlight bash %}
virsh # start Cent7Test
{% endhighlight %}

{% highlight bash %}
virsh # dominfo Cent7Test
Id:             5
Name:           Cent7Test
UUID:           17a23e76-ecf6-e0b6-f0d2-7c249d95b862
OS Type:        hvm
State:          running
CPU(s):         1
CPU time:       24.9s
Max memory:     1048576 KiB
Used memory:    1048576 KiB
Persistent:     yes
Autostart:      disable
Managed save:   no
Security model: apparmor
Security DOI:   0
Security label: libvirt-17a23e76-ecf6-e0b6-f0d2-7c249d95b862 (enforcing)
{% endhighlight %}

list all domain virtual interfaces
{% highlight bash %}
virsh # domiflist Centos7
Interface  Type       Source     Model       MAC
-------------------------------------------------------
vnet1      bridge     br0        virtio      52:54:00:e3:b5:20
{% endhighlight %}

get network interface stats for a domain

{% highlight bash %}
virsh # domifstat Centos7 vnet1
vnet1 rx_bytes 27319
vnet1 rx_packets 333
vnet1 rx_errs 0
vnet1 rx_drop 0
vnet1 tx_bytes 4677
vnet1 tx_packets 30
vnet1 tx_errs 0
vnet1 tx_drop 0
{% endhighlight %}

