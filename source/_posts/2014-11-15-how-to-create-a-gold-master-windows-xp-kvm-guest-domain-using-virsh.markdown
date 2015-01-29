---
layout: post
title: "How to Create a Gold Master Windows XP KVM Guest Domain using virsh"
date: 2014-11-15 10:24:55 -0500
comments: true
categories: ["kvm", "virtualization", "windows"]
---
I'm going to recreate my windows xp domain and fix the mistakes I made earlier.  I thought that Windows XP didn't like the qcow2 format, even though I knew it shouldn't matter.  It turns out that Windows XP didn't like having the disk bus to be virtio, I removed that configuration option and it saw the disk when I booted off the install iso.  I also had to remove the virtio option from the network configuration setting.
<!-- more -->
Let's create the vm using virt-install:
{% highlight bash %}
root@kvm:/var/lib/libvirt/images# virt-install -n Gold-WinXP -r 512 /
--disk path=/var/lib/libvirt/images/Gold-WinXP.qcow2,size=40,format=qcow2 /
-c /var/lib/libvirt/images/iso/WinXP-oem.iso --accelerate /
--network network=default /
--connect=qemu:///system --vnc --noautoconsole -v /
--os-type=windows --os-variant=winxp
{% endhighlight %}
Quick format

Default install

Activate it when it asks

Download SP3 to your desktop (not the vm)

Put it in the Sites folder and make sure it's world readable

Hit it with ie in the virtual machine

Run from location

Install SP3 and reboot

Now you can go ahead and install all the updates

From here, I'm going to shutdown the vm and clone it, but I might have to reactive it.  To avoid this I'm going to copy C:\WINDOWS\SYSTEM32\WPA.DBL to the desktop.  If I have to reactivate then I'll just copy this file back.
Start -> Run
{% highlight bash %}
copy C:WINDOWS\SYSTEM32\WPA.DBL Desktop
{% endhighlight %}

Turn of the virtual machine

Clone the Gold Master image, it'll look like it's going to take an hour but it won't:
{% highlight bash %}
root@kvm:/var/lib/libvirt/images# virt-clone --connect=qemu:///system -o Gold-WinXP /
-n WinXP -f WinXP.qcow2
Allocating 'WinXP.qcow2'                                                     |  40 GB     03:53     

Clone 'WinXP' created successfully.
{% endhighlight %}
Turn it back on:
{% highlight bash %}
virsh # start WinXP
Domain WinXP started
{% endhighlight %}
Connect to it with your vnc client, I use chicken.  The cloned image is usable and activated, which I didn't think it would be.  VMware usually complains if you move/clone/copy an image and then you have to sysprep and reactivate.
