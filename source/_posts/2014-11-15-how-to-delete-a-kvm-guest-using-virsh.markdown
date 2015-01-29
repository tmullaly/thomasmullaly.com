---
layout: post
title: "How to delete a KVM guest using virsh"
date: 2014-11-15 09:52:01 -0500
comments: true
categories: ["kvm", "virtualization"]
---
My virtualization needs are usually for sandboxing and learning.  I intended to start learning more devopsy ways of deploying but I have current needs to learn some new server and client software in a traditional desktop and server IT environment.  That's why I'm reverting back in my posts to making a gold master image of a server or desktop and cloning it.
<!-- more -->
This post is how to remove a kvm guest after you don't need it anymore.  My example is my windows xp image I built two days ago that I didn't use the qcow2 disk format for and instead used img.  Which is fine except I want to do fun things with qcow2 like snapshotting.

First let's get in virsh and shutdown the domain:
{% highlight bash %}
root@kvm:~# virsh
virsh # shutdown WinXP
Domain WinXP is being shutdown
{% endhighlight %}
Now make sure it isn't running:
{% highlight bash %}
virsh # list --all
 Id    Name                           State
----------------------------------------------------
 2     Ansible                        running
 4     piaf                           running
 14    Win81                          running
 15    OpenVPN                        running
 -     WinXP                          shut off
{% endhighlight %}
If the vm is still running and is stuck use "destroy" instead of shutdown in the command above and check it again with "list --all"

Two things need to happen next, the guest needs to be taken out of the kvm xml configuration and then the files need to be removed from the filesystem.

1) First let's undefine the guest domain:
{% highlight bash %}
virsh # undefine WinXP
Domain WinXP has been undefined
{% endhighlight %}
2) Now let's remove the disk image from the pool:
{% highlight bash %}
virsh # vol-delete --pool default WinXP.img
Vol WinXP.img deleted
{% endhighlight %}