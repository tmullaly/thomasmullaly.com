---
layout: post
title: "Add disks to LVM"
date: 2014-10-09 11:52:35 -0400
comments: true
categories: ["linux", "storage", "virtualization", "kvm"]
---
I haven't been working on my new kvm virtualization setup in over a month becasue I've been working on my condo to sell in the spring.  I took down my entire physical work enviroment to paint the walls and also as a basic cleaning out measure.  I finally moved my old mac pro to a place where I can get it on the network without running any cables and I've begun where I've left off last.  

The mac pro, as you remember, had ubuntu linux instaled on it and it has become my kvm host.  There are three disks in the machine, I've only been using one until now.  I want to add the other disks and I want the best performance and I'm not concerned about redundancy, so I've decided to stripe the disks.  First off let's look at the current environment:

<!-- more -->

{% highlight bash %}
root@kvm:~# df -h
Filesystem                Size  Used Avail Use% Mounted on
/dev/mapper/kvm--vg-root  283G   44G  225G  17% /
none                      4.0K     0  4.0K   0% /sys/fs/cgroup
udev                      5.4G  4.0K  5.4G   1% /dev
tmpfs                     1.1G  676K  1.1G   1% /run
none                      5.0M     0  5.0M   0% /run/lock
none                      5.4G     0  5.4G   0% /run/shm
none                      100M     0  100M   0% /run/user
/dev/sda2                 237M   37M  188M  17% /boot
{% endhighlight %}

I've go two more disks, you can see them with a fdisk -l.  I won't show all of the output here, but I will show you one of my disks (/dev/sdc), first I delete the current partition(s) then I create a new partition.
{% highlight bash %}
root@kvm:~# fdisk /dev/sdc

WARNING: GPT (GUID Partition Table) detected on '/dev/sdc'! The util fdisk doesn't support GPT. Use GNU Parted.


Command (m for help): p

Disk /dev/sdc: 320.1 GB, 320072933376 bytes
255 heads, 63 sectors/track, 38913 cylinders, total 625142448 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1               1   625142447   312571223+  ee  GPT

Command (m for help): d
Selected partition 1

Command (m for help): p

Disk /dev/sdc: 320.1 GB, 320072933376 bytes
255 heads, 63 sectors/track, 38913 cylinders, total 625142448 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

   Device Boot      Start         End      Blocks   Id  System

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-625142447, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-625142447, default 625142447): 
Using default value 625142447

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
{% endhighlight %}

Let's take a look at the current volume group, if this doesn't work for you then LVM is not being used.
{% highlight bash %}
root@kvm:~# vgdisplay
  --- Volume group ---
  VG Name               kvm-vg
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               297.85 GiB
  PE Size               4.00 MiB
  Total PE              76249
  Alloc PE / Size       76245 / 297.83 GiB
  Free  PE / Size       4 / 16.00 MiB
  VG UUID               dDekzp-hOYb-0iHn-xvwR-JMpG-C7tE-4xqtti
{% endhighlight %}

Now let's extend the volume group over the two new disks:
{% highlight bash %}
root@kvm:~# vgextend kvm-vg /dev/sdb1
  No physical volume label read from /dev/sdb1
  Physical volume "/dev/sdb1" successfully created
  Volume group "kvm-vg" successfully extended
root@kvm:~# vgextend kvm-vg /dev/sdc1
  No physical volume label read from /dev/sdc1
  Physical volume "/dev/sdc1" successfully created
  Volume group "kvm-vg" successfully extended
{% endhighlight %}

Let's check df -h again:
{% highlight bash %}
root@kvm:~# df -h
Filesystem                Size  Used Avail Use% Mounted on
/dev/mapper/kvm--vg-root  283G   44G  225G  17% /
none                      4.0K     0  4.0K   0% /sys/fs/cgroup
udev                      5.4G  4.0K  5.4G   1% /dev
tmpfs                     1.1G  676K  1.1G   1% /run
none                      5.0M     0  5.0M   0% /run/lock
none                      5.4G     0  5.4G   0% /run/shm
none                      100M     0  100M   0% /run/user
/dev/sda2                 237M   37M  188M  17% /boot
{% endhighlight %}

Wait, it's not there, what's going on?  Let's run lvdisplay:
{% highlight bash %}
root@kvm:~# lvdisplay
  --- Logical volume ---
  LV Path                /dev/kvm-vg/root
  LV Name                root
  VG Name                kvm-vg
  LV UUID                V5jemW-z51Z-4Mlx-zyR2-ZAtJ-Hzcz-ETmKP6
  LV Write Access        read/write
  LV Creation host, time kvm, 2014-08-06 20:38:36 -0400
  LV Status              available
  # open                 1
  LV Size                286.84 GiB
  Current LE             73431
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:0
   
  --- Logical volume ---
  LV Path                /dev/kvm-vg/swap_1
  LV Name                swap_1
  VG Name                kvm-vg
  LV UUID                MHvSXV-OhFS-YSWs-6C8Z-SeIL-xMpS-1anUC1
  LV Write Access        read/write
  LV Creation host, time kvm, 2014-08-06 20:38:36 -0400
  LV Status              available
  # open                 2
  LV Size                10.99 GiB
  Current LE             2814
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:1
{% endhighlight %}

{% highlight bash %}
root@kvm:~# lvextend /dev/kvm-vg/root /dev/sdb1
  Extending logical volume root to 287.32 GiB
  Logical volume root successfully resized
root@kvm:~# lvextend /dev/kvm-vg/root /dev/sdc1
  Extending logical volume root to 585.41 GiB
  Logical volume root successfully resized
{% endhighlight %}

{% highlight bash %}
root@kvm:~# lvdisplay
  --- Logical volume ---
  LV Path                /dev/kvm-vg/root
  LV Name                root
  VG Name                kvm-vg
  LV UUID                V5jemW-z51Z-4Mlx-zyR2-ZAtJ-Hzcz-ETmKP6
  LV Write Access        read/write
  LV Creation host, time kvm, 2014-08-06 20:38:36 -0400
  LV Status              available
  # open                 1
  LV Size                585.41 GiB
  Current LE             149865
  Segments               3
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:0
   
  --- Logical volume ---
  LV Path                /dev/kvm-vg/swap_1
  LV Name                swap_1
  VG Name                kvm-vg
  LV UUID                MHvSXV-OhFS-YSWs-6C8Z-SeIL-xMpS-1anUC1
  LV Write Access        read/write
  LV Creation host, time kvm, 2014-08-06 20:38:36 -0400
  LV Status              available
  # open                 2
  LV Size                10.99 GiB
  Current LE             2814
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:1
{% endhighlight %}
Good, it's there, let's try df -h again:
{% highlight bash %}
root@kvm:~# df -h
Filesystem                Size  Used Avail Use% Mounted on
/dev/mapper/kvm--vg-root  283G   44G  225G  17% /
none                      4.0K     0  4.0K   0% /sys/fs/cgroup
udev                      5.4G  4.0K  5.4G   1% /dev
tmpfs                     1.1G  676K  1.1G   1% /run
none                      5.0M     0  5.0M   0% /run/lock
none                      5.4G     0  5.4G   0% /run/shm
none                      100M     0  100M   0% /run/user
/dev/sda2                 237M   37M  188M  17% /boot
{% endhighlight %}
Dammit it's still not there.  We need to run resize2fs:
{% highlight bash %}
root@kvm:~# resize2fs /dev/kvm-vg/root
resize2fs 1.42.9 (4-Feb-2014)
Filesystem at /dev/kvm-vg/root is mounted on /; on-line resizing required
old_desc_blocks = 18, new_desc_blocks = 37
The filesystem on /dev/kvm-vg/root is now 153461760 blocks long.
{% endhighlight %}
df -h one more time:
{% highlight bash %}
root@kvm:~# df -h
Filesystem                Size  Used Avail Use% Mounted on
/dev/mapper/kvm--vg-root  577G   44G  507G   8% /
none                      4.0K     0  4.0K   0% /sys/fs/cgroup
udev                      5.4G  4.0K  5.4G   1% /dev
tmpfs                     1.1G  676K  1.1G   1% /run
none                      5.0M     0  5.0M   0% /run/lock
none                      5.4G     0  5.4G   0% /run/shm
none                      100M     0  100M   0% /run/user
/dev/sda2                 237M   37M  188M  17% /boot
{% endhighlight %}
Perfect.
