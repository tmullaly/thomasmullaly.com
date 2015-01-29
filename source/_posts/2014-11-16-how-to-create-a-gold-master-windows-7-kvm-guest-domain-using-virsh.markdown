---
layout: post
title: "How to Create a Gold Master Windows 7 KVM Guest Domain using virsh"
date: 2014-11-16 11:48:15 -0500
comments: true
categories: ["kvm", "virtualization", "windows"]
---
{% highlight bash %}
root@kvm:/var/lib/libvirt/images# virt-install -n Gold-Win7 -r 1024 /
--disk path=/var/lib/libvirt/images/Gold-Win7.qcow2,size=40,format=qcow2 /
-c /var/lib/libvirt/images/iso/7600.16385.090713-1255_x64fre_enterprise_en-us_EVAL_Eval_Enterprise-GRMCENXEVAL_EN_DVD.iso /
--accelerate --network network=default --connect=qemu:///system --vnc /
--noautoconsole -v --os-type=windows --os-variant=win7

Starting install...
Allocating 'Gold-Win7.qcow2'                              |  40 GB     00:00     
Creating domain...                                        |    0 B     00:01     
Domain installation still in progress. Waiting for installation to complete.

{% endhighlight %}