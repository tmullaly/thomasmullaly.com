---
layout: post
title: "Adding new esxi thin disk to ubuntu linux 14.04"
date: 2015-06-06 13:36:48 -0400
comments: true
categories: ["linux", "ubuntu", "vmware"]
---
I've been rsyncing the server data to a local vm in our esx cluster then backing up the vm.  I ran out of disk so here are my notes to add a new thin provisioned disk.

I had mounted a 100 GB virtual disk on /backups, now I'm replacing it with a 300 GB virtual disk.
<!-- more -->

* shut down the virtual machine
* added new 300 gb thin disk using vSphere
* power the virtual machine up
* login
* mkdir /backups-old
* umount /backups
* mount /dev/sdb1 /backups-old
{% highlight bash %}
root@itserver:~# fdisk /dev/sdc
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x6cac81a9.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): p

Disk /dev/sdc: 322.1 GB, 322122547200 bytes
255 heads, 63 sectors/track, 39162 cylinders, total 629145600 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x6cac81a9

   Device Boot      Start         End      Blocks   Id  System

Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): 
Using default response p
Partition number (1-4, default 1): 
Using default value 1
First sector (2048-629145599, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-629145599, default 629145599): 
Using default value 629145599

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
{% endhighlight %}

{% highlight bash %}
root@itserver:~# mkfs.ext4 /dev/sdc1 
mke2fs 1.42.9 (4-Feb-2014)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
19660800 inodes, 78642944 blocks
3932147 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=4294967296
2400 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done   
{% endhighlight %}

{% highlight bash %}  
root@itserver:~# mount /dev/sdc1 /backups
{% endhighlight %}

{% highlight bash %}
root@itserver:~# mount
/dev/sda1 on / type ext4 (rw,errors=remount-ro)
proc on /proc type proc (rw,noexec,nosuid,nodev)
sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
none on /sys/fs/cgroup type tmpfs (rw)
none on /sys/fs/fuse/connections type fusectl (rw)
none on /sys/kernel/debug type debugfs (rw)
none on /sys/kernel/security type securityfs (rw)
udev on /dev type devtmpfs (rw,mode=0755)
devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=0620)
tmpfs on /run type tmpfs (rw,noexec,nosuid,size=10%,mode=0755)
none on /run/lock type tmpfs (rw,noexec,nosuid,nodev,size=5242880)
none on /run/shm type tmpfs (rw,nosuid,nodev)
none on /run/user type tmpfs (rw,noexec,nosuid,nodev,size=104857600,mode=0755)
none on /sys/fs/pstore type pstore (rw)
/dev/sdb1 on /backups-old type ext4 (rw)
/dev/sdc1 on /backups type ext4 (rw)
{% endhighlight %}

{% highlight bash %}
root@itserver:~# vi /etc/fstab
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=5983a692-a8a0-4b8d-a5b0-45143fc6b816 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=dad51df4-05e7-4eae-900d-49483ed33b3c none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
# backup disk
/dev/sdb1	/backups-old	ext4	defaults	1 2
/dev/sdc1	/backups	ext4	defaults	1 2
{% endhighlight %}

now reboot to test

