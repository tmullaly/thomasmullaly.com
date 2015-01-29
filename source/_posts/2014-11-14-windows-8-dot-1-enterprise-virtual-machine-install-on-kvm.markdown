---
layout: post
title: "Windows 8.1 Enterprise Virtual Machine Install on KVM"
date: 2014-11-14 13:27:02 -0500
comments: true
categories: ["kvm", "virtualization", "windows"]
---
First download the evaluation version of Windows 8.1 Enterprise

Now download the fedora storage drivers from the kvm site
<!-- more -->
{% highlight bash %}
tom@kvm:~$ wget http://alt.fedoraproject.org/pub/alt/virtio-win/latest/images/virtio-win-0.1-81.iso
--2014-11-14 13:22:18--  http://alt.fedoraproject.org/pub/alt/virtio-win/latest/images/virtio-win-0.1-81.iso
Resolving alt.fedoraproject.org (alt.fedoraproject.org)... 209.132.181.24, 209.132.181.25, 209.132.181.26, ...
Connecting to alt.fedoraproject.org (alt.fedoraproject.org)|209.132.181.24|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 74143744 (71M) [application/octet-stream]
Saving to: ‘virtio-win-0.1-81.iso’

100%[==========================================================>] 74,143,744   429KB/s   in 2m 49s 

2014-11-14 13:25:08 (429 KB/s) - ‘virtio-win-0.1-81.iso’ saved [74143744/74143744]
{% endhighlight %}

Now move it into place:
{% highlight bash %}
tom@kvm:~$ sudo mv virtio-win-0.1-81.iso /var/lib/libvirt/images/
{% endhighlight %}

Transfer the iso to the kvm host and put it in /var/lib/libvirt/images

Create the virtual machine
{% highlight bash %}
root@kvm:/var/lib/libvirt/images# virt-install -n Win81 -r 2048 --disk path=/var/lib/libvirt/images/Win81.qcow2,bus=virtio,size=40,format=qcow2 -c /var/lib/libvirt/images/9600.17050.WINBLUE_REFRESH.140317-1640_X64FRE_ENTERPRISE_EVAL_EN-US-IR3_CENA_X64FREE_EN-US_DV9.ISO --accelerate --network network=default,model=virtio --connect=qemu:///system --vnc --noautoconsole -v --os-type=windows --os-variant=win7

Starting install...
Allocating 'Win81.qcow2'                                                     |  40 GB     00:00     
Creating domain...                                                           |    0 B     00:01     
Domain installation still in progress. Waiting for installation to complete.
{% endhighlight %}
When the install gets stuck on the storage page, eject the windows 8.1 install dvd and insert the driver iso:
{% highlight bash %}
virsh # domblklist Win81
Target     Source
------------------------------------------------
vda        /var/lib/libvirt/images/Win81.qcow2
hdc        /var/lib/libvirt/images/9600.17050.WINBLUE_REFRESH.140317-1640_X64FRE_ENTERPRISE_EVAL_EN-US-IR3_CENA_X64FREE_EN-US_DV9.ISO

virsh # change-media Win81 hdc --eject
succeeded to complete action eject on media

virsh # change-media Win81 hdc /var/lib/libvirt/images/virtio-win-0.1-81.iso --insert
succeeded to complete action insert on media
{% endhighlight %}
You'll see three drivers, a network driver, a scsi driver and a balloon driver, choose the scsi driver.

Now you'll have to put the install disk back in:
{% highlight bash %}
virsh # change-media Win81 hdc /var/lib/libvirt/images/virtio-win-0.1-81.iso --eject
succeeded to complete action eject on media

virsh # change-media Win81 hdc /var/lib/libvirt/images/9600.17050.WINBLUE_REFRESH.140317-1640_X64FRE_ENTERPRISE_EVAL_EN-US-IR3_CENA_X64FREE_EN-US_DV9.ISO --insert
succeeded to complete action insert on media
{% endhighlight %}

Update the Network driver
change the cdrom again:
{% highlight bash %}
virsh # change-media Win81 hdc --eject
succeeded to complete action eject on media

virsh # change-media Win81 hdc /var/lib/libvirt/images/virtio-win-0.1-81.iso --insert
succeeded to complete action insert on media
{% endhighlight %}
Choose the Red Hat Virtio Ethernet Adapter driver and PCI Adapter (Balloon Driver)
