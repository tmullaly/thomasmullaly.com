---
layout: post
title: "How to Create a Kali Linux KVM Guest Domain using virsh"
date: 2015-01-04 11:04:05 -0500
comments: true
categories: ["linux", "kvm", "security", "kali"]
---
Here are my instructions for installing Kali linux in a kvm domain.
<!-- more -->
{% highlight bash %}
root@kvm:/var/lib/libvirt/images/iso# virt-install -n Kali -r 2048 /
--disk path=/var/lib/libvirt/images/Kali.qcow2,bus=virtio,size=50,format=qcow2 /
-c /var/lib/libvirt/images/iso/kali-linux-1.0.9a-amd64.iso /
--network network=default,model=virtio --connect=qemu:///system --vnc /
--noautoconsole --hvm --video=vmvga --os-type=linux --os-variant=debianwheezy

Starting install...
Allocating 'Kali.qcow2'                                                 |  50 GB     00:00     
Creating domain...                                                      |    0 B     00:00     
Domain installation still in progress. You can reconnect to 
the console to complete the installation process.
{% endhighlight %}
Now Connect with your VNC client.

I had problems with booting into graphical mode because of my old system, so I booted into single user mode.  From single usermode I added the chkconfig program and disabled gdm3 from starting.  I also created a sym-link from /usr/sbin/gdm3 to /usr/bin/startx, now it's like the old backtrack where you login to the console and only start up x if you need it.
{% highlight bash %}
apt-get install chkconfig -y
ln -s /usr/sbin/gdm3 /usr/bin/startx
chkconfig -l
chkconfig --del gdm3
reboot
{% endhighlight %}
Now you can update and upgrade:
{% highlight bash %}
apt-get update
apt-get upgrade
{% endhighlight %}

Install HTOP and nethogs, HTOP shows running process and memory used including many more details. (you could use top command, but HTOP is just more useful). NetHogs shows traffic used by applications per interface. Install them using the following command:
{% highlight bash %}
apt-get install htop nethogs -y
{% endhighlight %}
You can now run then using the following commands:
{% highlight bash %}
htop
nethogs eth0
nethogs wlan0
{% endhighlight %}

Fix the graphics, if vmvga didn't work, in my case the default cirrus needed to be changed.
{% highlight bash %}
virsh dumpxml Kali > /tmp/Kali.xml
vi /tmp/Kali.xml 
{% endhighlight %}
Change cirrus to vmvga
{% highlight bash %}
virsh define /tmp/Kali.xml 
Domain Kali defined from /tmp/Kali.xml

tom@kvm:~$ virsh start Kali
Domain Kali started
{% endhighlight %}
