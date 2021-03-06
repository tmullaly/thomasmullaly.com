---
layout: post
title:  "Find Number of CPU Cores Command"
date:   2012-10-19
categories: linux
---
How do I find out the number of CPU cores available under HP x86_64 Linux serer running on RHEL / Debian Linux 6.x?

You can use the lscpu or nproc command to display the number of processing units available to the current process, which may be less than the number of online processors (please note that not all server supports hotplug a CPU on a running Linux system).


The proc file system is a pseudo-file system which is used as an interface to kernel data structures. It is commonly mounted at /proc. The /proc/cpuinfo file is nothing but a collection of CPU and system architecture dependent items, for each supported architecture a different list. Two common entries are processor which gives CPU number and bogomips; a system constant that is calculated during kernel initialization. SMP machines have information for each CPU.

###nproc Example

The nproc command shows the number of processing units available:
{% highlight bash %}
nproc
{% endhighlight %}
Sample outputs:

  8

###lscpu Command

lscpu gathers CPU architecture information form /proc/cpuinfon in human-read-able format:
{% highlight bash %}
lscpu
{% endhighlight %}

Sample outputs, vm71 and cyan:
{% highlight bash %}
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                1
On-line CPU(s) list:   0
Thread(s) per core:    1
Core(s) per socket:    1
Socket(s):             1
NUMA node(s):          1
Vendor ID:             AuthenticAMD
CPU family:            15
Model:                 65
Stepping:              2
CPU MHz:               2593.109
BogoMIPS:              5186.21
L1d cache:             64K
L1i cache:             64K
L2 cache:              1024K
NUMA node0 CPU(s):     0
</code>
<code>
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 42
Stepping:              7
CPU MHz:               1600.000
BogoMIPS:              6784.26
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              8192K
NUMA node0 CPU(s):     0-7
{% endhighlight %}

###/proc/cpuinfo
The /proc/cpuinfo and sysfs stores info about your CPU architecture ike number of CPUs, threads, cores, sockets, NUMA nodes, information about CPU caches, CPU family, model, bogoMIPS, yte order and much more:
{% highlight bash %}
less /proc/cpuinfo
{% endhighlight %}

