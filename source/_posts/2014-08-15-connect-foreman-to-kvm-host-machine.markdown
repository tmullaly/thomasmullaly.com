---
layout: post
title: "Connect foreman to kvm host machine"
date: 2014-08-15 12:24:46 -0400
comments: true
categories: ["foreman", "virtualization", "kvm", "puppet"]
---
We need to setup ssh keys so that the foreman vm can communicate with the host operating system.  Becasue we're running Ubuntu, the root account is disabled by default so I'm going to enable it by giving it a password.  Then we're going to create an ssh key on the foreman vm and transfer it to the kvm host.  We'll test the ssh key and then we'll use the foremen web interface to add the ability to provision a kvm instance.

First log into the kvm host machine and sudo to root (sudo -i).
<!-- more -->
Create a password for root:
{% highlight bash %}
passwd root
{% endhighlight %}

Now, let's enable the ability of root to login via ssh
{% highlight bash %}
vi /etc/ssh/sshd_config
{% endhighlight %}

Change:
{% highlight bash %}
PermitRootLogin without-password
{% endhighlight %}
to
{% highlight bash %}
PermitRootLogin yes
{% endhighlight %}

Now restart sshd:

{% highlight bash %}
service ssh restart
{% endhighlight %}

Let's log into the foreman virtual machine and set up the ssh keys.
http://theforeman.org/manuals/1.6/index.html#5.2.5LibvirtNotes
{% highlight bash %}
root# mkdir /usr/share/foreman/.ssh
root# chmod 700 /usr/share/foreman/.ssh
root# chown foreman:foreman /usr/share/foreman/.ssh
root# su foreman -s /bin/bash
foreman$ ssh-keygen
foreman$ ssh-copy-id root@hostname.com
foreman$ ssh root@hostname.com
exit
{% endhighlight %}

Test the connection:
{% highlight bash %}
virsh -c qemu+ssh://kvm.thunderhouse.com/system list
{% endhighlight %}

Now go to the foreman web application:

Infrastructure -> Compute Resources -> New Compute Resource

Name: KVM-Host

Provider: Libvirt

URL: qemu+ssh://root@kvm.thunderhouse.com/system

Display type: VNC

Console: Checked (yes)

Test the connection and if it works then hit Submit.

Now we can provision a new KVM guest through foreman.