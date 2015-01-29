---
layout: post
title: "PBX in a Flash on CentOS 6.5 on KVM"
date: 2014-11-10 10:02:31 -0500
comments: true
categories: ["virtualization", "kvm", "voip", "centos", "linux"]
---
Let's start off by downloading the CentOS 6.5 minimal iso.
<!-- more -->
{% highlight bash %}
root@kvm:~# virt-install -n CentOS65 -r 1024 / 
--disk path=/var/lib/libvirt/images/CentOS65.qcow2,bus=virtio,size=10,format=qcow2 /
-c /var/lib/libvirt/images/CentOS-6.5-x86_64-minimal.iso / 
--accelerate --network network=default,model=virtio / 
--connect=qemu:///system --vnc --noautoconsole -v /
--os-type=linux --os-variant=rhel6

Starting install...
Allocating 'CentOS65.qcow2'                                                           |  10 GB     00:00     
Creating domain...                                                                    |    0 B     00:00     
Domain installation still in progress. You can reconnect to 
the console to complete the installation process.
{% endhighlight %}

{% highlight bash %}
root@kvm:/var/lib/libvirt/images# virt-clone --connect=qemu:///system -o CentOS65 -n piaf -f piaf.qcow2
Allocating 'piaf.qcow2'                                                               |  10 GB     00:02     

Clone 'piaf' created successfully.
{% endhighlight %}

{% highlight bash %}
virsh # start piaf
Domain piaf started
{% endhighlight %}

Change into the network-scripts directory
{% highlight bash %}
cd /etc/sysconfig/network-scripts
{% endhighlight %}

Generate a new uuid for the network interface and appended it to the ifcfg-eth0 file
{% highlight bash %}
uuidgen >> ifcfg-eth0
{% endhighlight %}

Get the mac address of eth1
{% highlight bash %}
ifconfig -a
{% endhighlight %}

Edit the ifcfg-eth0 file
{% highlight bash %}
vi ifcfg-eth0
  1. fix the uuid
  2. fix the mac address
  3. change ONBOOT to yes
{% endhighlight %}

Remove 70-persistent-net.rules, it will be regenerated automatically
{% highlight bash %}
rm -f /etc/udev/rules.d/70-persistent-net.rules
{% endhighlight %}

Change the hostname
{% highlight bash %}
vi /etc/network
{% endhighlight %}

Reboot the VM
{% highlight bash %}
reboot
{% endhighlight %}

Log back in and run a yum update
{% highlight bash %}
yum update
{% endhighlight %}
Reboot

Get the piaf3-install script and run it.

