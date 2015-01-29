---
layout: post
title: "My new virtualization platform - KVM on Ubuntu 14.04.1 on my old 2006 Mac Pro"
date: 2014-08-07 07:32:51 -0400
comments: true
categories: virtualization puppet devops
---
My old Mac Pro is awesome, but I didn't really want to contine using OSX 10.6 and VMware Fusion 3.0.  So I wanted to reinstall a latest linux distro on it, but ran into some difficulty because of the efi thing.  On my Mac pro the EFI is 32 bits which prevents booting a 64-bit bootloader, or something like that.  When I tried a normal boot disk for CentOS 7 I got this:
{% highlight bash %}
1.
2.
Select CD-ROM Boot type:
{% endhighlight %}
<!-- more -->
So I needed to download a special +mac CD:
{% highlight bash %}
wget http://cdimage.ubuntu.com/releases/14.04/release/ubuntu-14.04.1-server-amd64+mac.iso
{% endhighlight %}
I burned that iso to a CD and booted of it.
I ran through the server installer only choosing OpenSSH Server and Virtualization Host to be installed.
I also chose to use LVM, thinking that I can put the four drives in the Mac Pro is a big IO-loadbalanced stripe.
By choosing the Virtualiztion Host group on install created a netowrk interface virbr0.  The virbr0, or "Virtual Bridge 0" interface is used for NAT (Network Address Translation). It is provided by the libvirt library, and virtual environments sometimes use it to connect to the outside network.

After an apt-get update and apt-get upgrade, the Mac Pro was looking pretty good. Now it's time to install the first VM.  I downloaded the regular ubuntu server disk: 
{% highlight bash %}
wget http://mirrors.ccs.neu.edu/releases.ubuntu.com/14.04.1/ubuntu-14.04.1-server-amd64.iso
wget http://mirrors.ccs.neu.edu/releases.ubuntu.com/14.04.1/ubuntu-14.04.1-desktop-amd64.iso
{% endhighlight %}

But then I thought, wait a minute my first vm should be the Foreman, which only supports the following:

* Red Hat Enterprise Linux 6 and derivatives (CentOS, Scientific Linux)
  * EPEL is required
  * On RHEL 6, additionally the Optional and RHSCL 1.0 repositories/channels:
    * yum-config-manager --enable rhel-6-server-optional-rpms rhel-server-rhscl-6-rpms
    * check the above repositories because the command can silently fail when subscription does not provide it: yum repolist
* Fedora 19
* Debian 7 (Wheezy)
* Debian 6 (Squeeze) (update Puppet from backports)
* Ubuntu 14.04 (Trusty)
* Ubuntu 12.04 (Precise)

{% highlight bash %}
wget http://mirror.rackspace.com/CentOS/6.5/isos/x86_64/CentOS-6.5-x86_64-minimal.iso
{% endhighlight %}
and...
{% highlight bash %}
wget http://cdimage.debian.org/debian-cd/7.6.0/amd64/iso-cd/debian-7.6.0-amd64-netinst.iso
{% endhighlight %}

virt-install is part of the virtinst package. To install it, from a terminal prompt enter:
{% highlight bash %}
sudo apt-get install virtinst
{% endhighlight %}

Let's create a new vm for foreman:
{% highlight bash %}
virt-install -n foreman -r 1024 --disk \ 
path=/var/lib/libvirt/images/foreman.img,bus=virtio,size=8 \
-c /var/lib/libvirt/images/CentOS-6.5-x86_64-minimal.iso \
--accelerate --network network=default,model=virtio \ 
--connect=qemu:///system --vnc --noautoconsole -v
{% endhighlight %}

1. -n foreman: the name of the new virtual machine will be web_devel in this example.
2. -r 1024: specifies the amount of memory the virtual machine will use in megabytes.
3. --disk path=/var/lib/libvirt/images/foreman.img,size=8: indicates the path to the virtual disk which can be a file, partition, or logical volume. In this example a file named web_devel.img in the /var/lib/libvirt/images/ directory, with a size of 8 gigabytes, and using virtio for the disk bus.
4. -c /var/lib/libvirt/images/CentOS-6.5-x86_64-minimal.iso: file to be used as a virtual CDROM. The file can be either an ISO file or the path to the host's CDROM device.
5. --accelerate: enables the kernel's acceleration technologies.
6. --network provides details related to the VM's network interface. Here the default network is used, and the interface model is configured for virtio.
7. --vnc: exports the guest's virtual console using VNC.
8. --noautoconsole: will not automatically connect to the virtual machine's console.
9. -v: creates a fully virtualized guest.

Fire it up:
{% highlight bash %}
virsh start foreman
{% endhighlight %}

Connect with your vnc client by ssh'ing to the kvm host and connecting to port 0

Now, login as root, edit /etc/sysconfig/netowrk-scripts/ifcfg-eth0 and change onboot to equal yes.

Restart netowrking, service network restart

yum update / yum upgrade

CentOS/RedHat doesn't install acpid for some reason and we need it to shutdown from the virsh.
Log back in and yum install acpid

reboot.

Now let's go on to connect this new foreman vm installation to the kvm host:
<a href="/2014/08/15/connect-foreman-to-kvm-host-machine/">Connect the foreman vm to the kvm host</a>



