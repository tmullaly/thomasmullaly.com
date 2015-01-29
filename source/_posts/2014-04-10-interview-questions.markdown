---
layout: post
title:  "Interview Questions"
date:   2014-04-10 19:57:30
categories: linux
---
# Unix Process

## What is the difference between a process and a thread?
<!-- more -->

A thread is a lightweight process.  Each process has a separate stack, text, data and heap. Threads have their own stack, but share text, data and heap with the process. Text is the actual program itself, data is the input to the program and heap is the memory which stores files, locks, sockets. Reference: 
<a href="https://computing.llnl.gov/tutorials/pthreads/#Thread" rel="nofollow">https://computing.llnl.gov/tutorials/pthreads/#Thread</a>

## What is a zombie process?
A zombie process is a one which has completed execution, however it's entry is still in the process table to allow the parent to read the child's exit status. The reason the process is a zombie is because it is "dead" but not yet "reaped" by it's parent.  Parent processes normally issue the wait system call to read the child's exit status whereupon the zombie is removed. The kill command does not work on zombie process. When a child dies the parent receives a SIGCHLD signal.
	
Reference: <a href="http://en.wikipedia.org/wiki/Zombie_process" rel="nofollow">http://en.wikipedia.org/wiki/Zombie_process</a>

## Describe ways of process inter-communication

*  POSIX mmap, message queues, semaphores, and Shared memory
*  Anonymous pipes and named pipes
*  Unix domain sockets
*  RPC
*  For a complete list see <a href="http://en.wikipedia.org/wiki/Inter-process_communication" rel="nofollow">http://en.wikipedia.org/wiki/Inter-process_communication</a>

## How to daemonize a process

*  The fork() call is used to create a separate process.
*  The setsid() call is used to detach the process from the parent (normally a shell).
*  The file mask should be reset.
*  The current directory should be changed to something benign.
*  The standard files (stdin,stdout and stderr) need to be reopened.

## Describe how processes executes in a Unix shell

Let's take the example of /bin/ls. When run "ls" the shell searches in it's path for an executable named "ls", when it finds it, the shell will forks off a copy of itself using the fork system call. If the fork succeeds, then in the child process the shell will run "exec /bin/ls" which will replace the copy of the child shell wit itself.  Any parameters that that are passed to "ls" are done so by exec.

## When you send a kill -9 to a process, you notice that it does not die, what could have happened?
During critical section execution, some processes can setup signal blocking.  The system call to mask signals is "sigprocmask". When the kernel raises a blocked signal, it is not delivered. Such signals are called pending.  When a pending signal is unblocked, the kernel passes it off to the process to handle.  It is possible that the process was masking -9.

## How do you end up with zombie processes?
Zombie processes are created when the parent does not reap the child. This can happen due to parent not executing the wait() system call after forking.

## What are Unix Signals?
Signals are an inter process communication method. The default signal in Linux is SIG-TERM. SIG-INT cannot be ignored and causes an application to be forcefully killed. Use the "kill" command to send signals to a process. Another popular signal is the "HUP" signal which is used to "reset" or "hang up" applications.


# Networking
## Define TCP slow start
Tcp slow start is a congestion control algorithm that starts by increasing the TCP congestion window each time an ACK is received, until an ACK is not received.
Reference:<a href="http://en.wikipedia.org/wiki/Slow-start" rel="nofollow">http://en.wikipedia.org/wiki/Slow-start</a>

## Name a few TCP connections states
1.  LISTEN - Server is listening on a port, such as HTTP
1.  SYNC-SENT - Sent a SYN request, waiting for a response
1.  SYN-RECEIVED - (Server) Waiting for an ACK, occurs after sending an ACK from the server
1.  ESTABLISHED - 3 way TCP handshake has completed

## Define the various protocol states of DHCP
-  DHCPDISCOVER 	client-&gt;server : broadcast to locate server</li>
-  DHCPOFFER   	server-&gt;client :  offer to client with offer of configuration parameters</li>
-  DHCPREQUEST 	client-&gt;server : requesting a dhcp config from server</li>
-  DHCPACK  		server-&gt;client : actual configuration paramters</li>
-  DHCPNAK  		server-&gt;client : indicating client&#8217;s notion of network address is incorrect</li>
-  DHCPDECLINE		client-&gt;server : address is already in use</li>
-  DHCPRELEASE		client-&gt;server : giving up of ip address</li>
-  DHCPINFORM		client-&gt;server : asking for local config parameters</li>
-  Reference: <a href="https://www.ietf.org/rfc/rfc2131.txt" rel="nofollow">https://www.ietf.org/rfc/rfc2131.txt</a>
	

## How do you figure out the network and broadcast address of a network given a netmask?

## Describe a TCP packet fields

## Difference between TCP/UDP
-  Reliable/Unreliable
-  Ordered/Unordered
-  Heavyweight/Lightweight
-  Streaming
-  Header size
-  Examples:

## What are the different kind of NAT available?
There is SNAT and DNAT. SNAT stands for source network address translation. DNAT stands for destination network address translation. SNAT occurs when the source IP address if RFC 1918 and is changed to be non-RFC 1918. For instance if you are at home using your cable model and want to connect to and external site such as <a href="http://www.cnn.com" rel="nofollow">http://www.cnn.com</a>, then your router will change the source address of the TCP packet to be it's external public IP. This is called SNAT. DNAT is when the destination IP address is changed. For instance when your packet reaches the <a href="http://www.cnn.com" rel="nofollow">http://www.cnn.com</a> router, and the web server behind the router is using RFC 1918 space, then the router might change the destination to be the RFC 1918 IP address of the web server. This is called DNAT.

# DNS

## Explain the SOA record in DNS
SOA stands for Start of Authority and it contains the following entries:
{% highlight bash %}
@   IN  SOA     nameserver.mycomaind.com.  postmaster.mydomain.com. (
                               1            ; serial number
                               3600         ; refresh   [1h]
                               600          ; retry     [10m]
                               86400        ; expire    [1d]
                               3600 )       ; min TTL   [1h]fire
{% endhighlight %}
**Serial number** should be refreshed each time a change is made to the zone file. This is how slave DNS servers know to pull a change from the master.

**Refresh** is the amount of time a slave DNS server should wait before pulling from the master.

**Retry** is how long a slave should wait before retrying to get a zone file if the initial retry fails.

**Expire** is how long a secondary server will keep trying to get a zone from the master. If this time expires before a successful zone transfer, the secondary will stop answering queries.

**TTL** is how long to keep the data in a zone file.

# Filesystems

## List open file handles

-  lsop -p process-id
-  Or ls /proc/process-id/fd

## What is an inode?
An inode is a data structure in Unix that contains metadata about a file. Some of the items contained in an inode are:

1.  mode
1.  owner (UID, GID)
1.  size
1.  atime, ctime, mtime
1.  acl's
1.  blocks list of where the data is
The filename is present in the parent directory's inode structure.
	
## What is the difference between a soft link and a hard link?
1) Hardlink shares the same inode number as the source link. Softlink has a different inode number. Example:
{% highlight bash %}
$ touch a
$ ln a b
$ ls -i a b
24 a  24 b
$ ln -s a c
$ ls -i a c
24 a  25 c
{% endhighlight %}
2) In the data portion of the softlink is the name of the source file.

3) Hardlinks are only valid in the same filesystem, softlinks can be across filesystems.

## When would you use a hardlink over a softlink?
A hardlink is useful when the source file is getting moved around, because renaming the source does not remove the hardlink connection. On the other hand, if you rename the source of a softlink, the softlink is broken. This is because hardlink's share the same inode, and softlink uses the source filename in it's data portion.

## Describe LVM and how it can be helpful
LVM stands for logical volume manager and it is a way of grouping disks into logical units. The basic unit of LVM is a PE or a physical extent. One disk may be divided into one or more PE's.  One or more PE's are contained in a VG or a volume group. Or or more LV or logical volumes are created out of a VG. For instance, if we have a server with 2x1TB disk drives, we can create 4xPE's on it, each one being 500GB. On disk 1 let's say we name the PE's PE1 and PE3 and on disk 2 we name the PE's PE2 and PE4. We can then create VG0 out of PE1 and PE2, and VG1 out of PE3 and PE4. After that we can create a LV called /root and another one called swap on VG0.

An advantage of using LVM is that we can create "software" RAID, i.e., we can join multiple disks into one bigger disk. We cannot select the RAID level with LVM, for instance we cannot say that a VG is of RAID 5 type, however we are able to pick and chose the different PE's we want in a VG. Also LVM allows for dynamically growing a disk.

## What is "md" and how do you use it?
MD is Linux software RAID. RAID can be done either in hardware wherein there is a RAID controller that does RAID and presents a logical volume to the OS, or RAID can be done in software wherein the kernel has a RAID driver which takes one or more disks can does RAID across them. "MD" refers to the software RAID component of Linux.

## What are some reasons to consider one filesystem type over another, such as XFS, ext?

## What is RAID, and define a few RAID levels

###RAID 0
RAID 0 comprises striping (but no parity or mirroring). This level provides no data redundancy nor fault tolerance, but improves performance through parallelism of read and write operations across multiple drives. RAID 0 has no error detection mechanism, so the failure of one disk causes the loss of all data on the array.

###RAID 1
RAID 1 comprises mirroring (without parity or striping). Data are written identically to two (or more) drives, thereby producing a "mirrored set". The read request is serviced by any of the drives containing the requested data. This can improve performance if data is read from the disk with the least seek latency and rotational latency. Conversely, write performance can be degraded because all drives must be updated; thus the write performance is determined by the slowest drive. The array continues to operate as long as at least one drive is functioning.

###RAID 2
RAID 2 comprises bit-level striping with dedicated Hamming-code parity. All disk spindle rotation is synchronized and data is striped such that each sequential bit is on a different drive. Hamming-code parity is calculated across corresponding bits and stored on at least one parity drive. This level is of historical significance only. Although it was used on some early machines (e.g. the Thinking Machines CM-2), it is only recently used by high-performance commercially available systems.

###RAID 3
RAID 3 comprises byte-level striping with dedicated parity. All disk spindle rotation is synchronized and data is striped such that each sequential byte is on a different drive. Parity is calculated across corresponding bytes and stored on a dedicated parity drive. Although implementations exist, RAID 3 is not commonly used in practice.

###RAID 4
RAID 4 comprises block-level striping with dedicated parity. This level was previously used by NetApp, but has now been largely replaced by a proprietary implementation of RAID 4 with two parity disks, called RAID-DP.

###RAID 5
RAID 5 comprises block-level striping with distributed parity. Unlike in RAID 4, parity information is distributed among the drives. It requires that all drives but one be present to operate. Upon failure of a single drive, subsequent reads can be calculated from the distributed parity such that no data is lost. RAID 5 requires at least three disks. RAID 5 is seriously affected by the general trends regarding array rebuild time and chance of failure during rebuild. In August 2012, Dell posted an advisory against the use of RAID 5 in any configuration and of RAID 50 with "Class 2 7200 RPM drives of 1 TB and higher capacity".

###RAID 6
RAID 6 comprises block-level striping with double distributed parity. Double parity provides fault tolerance up to two failed drives. This makes larger RAID groups more practical, especially for high-availability systems, as large-capacity drives take longer to restore. As with RAID 5, a single drive failure results in reduced performance of the entire array until the failed drive has been replaced. With a RAID 6 array, using drives from multiple sources and manufacturers, it is possible to mitigate most of the problems associated with RAID 5. The larger the drive capacities and the larger the array size, the more important it becomes to choose RAID 6 instead of RAID 5. RAID 10 also minimizes these problems.

###RAID 0+1
RAID 0+1 (also called RAID 01), is a RAID level used for both replicating and sharing data among disks. RAID 0+1 is a mirror of stripes. The usable capacity of a RAID 0+1 array is the same as a RAID 1 array, where half of the total capacity is used to mirror the other half. (N/2) * S<sub>min</sub>, where N is the total number of drives and S<sub>min</sub> is the capacity of the smallest drive in the array.

### RAID 1+0
A RAID 1+0, sometimes called RAID 1&0 or RAID 10, is similar to a RAID 0+1 with exception that the RAID levels used are reversed—RAID 10 is a stripe of mirrors.  

Wikipedia has a very well written on RAID here <a href="https://en.wikipedia.org/wiki/RAID" rel="nofollow">https://en.wikipedia.org/wiki/RAID</a>.

## If a filesystem is full, and you see a large file that is taking up a lot of space, how do make space on the filesystem?
1) If no process has the filehandle open, you can delete the file.

2) If a process has the filehandle open, it is better if you do not delete the file, instead you can "cp /dev/null" on the file, which will reduce it's size to 0.

3) A filesystem has a reserve, you can reduce the size of this reserve to create more space using tunefs.

#Algorithms

## Binary tree complexity
O(log n)
## Hash table complexity
O(1)

# HTTP

## Common Http response codes
-  200 OK The request has succeeded</li>
-  500 Internal Server Error (Server Error)</li>
-  403 Forbidden</li>
-  301 Permanent Redirect</li>
-  302 Temporary Redirect</li>
-  <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" rel="nofollow">http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html</a>

## What is an http cookie
-  Http cookie is a small piece of data that a server sends to a browser, which a browser usually stores in it's cookie cache. Cookie can be used to maintain session information since HTTP is stateless, and also for user preferences at a given site. Cookies can also be used to store encrypted password. Browsers send cookies back to the server when they make a connection.
-  <a href="http://en.wikipedia.org/wiki/HTTP_cookie" rel="nofollow">http://en.wikipedia.org/wiki/HTTP_cookie</a>

## Http methods
<ul>
<li>Http methods are ways of communicating between server and client. Common examples are http get and http put which is used by http forms for data exchange. Other methods include, post, head,  and connect. </li>
<li><a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html" rel="nofollow">http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html</a></li>
</ul>
<h5>Http headers</h5>
<ul>
<li>Http header fields are common components of HTTP requests and responses. Headers are colon separated name-value pairs in clear text. Some common headers are: Cache-control which specifies where to cache or not the contents of a page, Accept, which can be text/plain, Content-length which specifies the size of the content, Host, which is the domain name of the server.</li>
<li><a href="http://en.wikipedia.org/wiki/List_of_HTTP_header_fields" rel="nofollow">http://en.wikipedia.org/wiki/List_of_HTTP_header_fields</a></li>
</ul>
<hr />
<h3>Databases</h3>
<h5>What is the difference between MyISAM and InnoDB?</h5>
<p>MyISAM:<br />
1. Supports table level locking<br />
2. Is Faster than InnoDB<br />
3. Does not support foreign keys<br />
4. Stores table, data and index in different files<br />
5. Does not support transactions (no commit with rollback)<br />
6. Useful for more selects with fewer updates<br />
InnoDB:<br />
1. Support row level locking<br />
2. Is slower than MyISAM<br />
3. Supports foreign keys<br />
4. Stores table and index in table space<br />
5. Supports transactions</p>
<h5>What are some things to check on a slow database?</h5>
<p>MySQL is fairly popular, so let&#8217;s look at some basic MySQL debugging. First off, check the OS to make sure the system is running fine, specially check CPU, memory, SWAP space and disk I/O. Assuming those are all ok, then log into MySQL and check the running queries, you can do so by running the command <i>&#8216;show full processlist&#8217;</i>. This will give you a list of queries running on the server. If you see a query that has been running for an excessively long time, you should investigate that query. See <a href="https://dev.mysql.com/doc/refman/5.1/en/show-processlist.html" rel="nofollow">https://dev.mysql.com/doc/refman/5.1/en/show-processlist.html</a> for additional details.</p>
{% highlight bash %}
mysql> show full processlist;
+-----+------+-----------+-----------+---------+------+-------+-----------------------+
| Id  | User | Host      | db        | Command | Time | State | Info                  |
+-----+------+-----------+-----------+---------+------+-------+-----------------------+
| 865 | root | localhost | wordpress | Query   |   -1 | NULL  | show full processlist |
+-----+------+-----------+-----------+---------+------+-------+-----------------------+
1 row in set (0.00 sec)
{% endhighlight %}
To investigate queries use the command 'explain'. When investigating queries, if you notice the lack of a primary key you should investigate if having a primary key for that particular table makes sense. Having a key in general improves performance of a table. See <a href="https://dev.mysql.com/doc/refman/5.0/en/explain.html" rel="nofollow">https://dev.mysql.com/doc/refman/5.0/en/explain.html</a> for additional details.
{% highlight bash %}
mysql> explain select * from wp_posts;
+----+-------------+----------+------+---------------+------+---------+------+------+-------+
| id | select_type | table    | type | possible_keys | key  | key_len | ref  | rows | Extra |
+----+-------------+----------+------+---------------+------+---------+------+------+-------+
|  1 | SIMPLE      | wp_posts | ALL  | NULL          | NULL | NULL    | NULL |   19 |       |
+----+-------------+----------+------+---------------+------+---------+------+------+-------+
1 row in set (0.01 sec)
{% endhighlight %}
<p>Another item you should investigate is the slow query log file. If you look in <i>/etc/mysql/my.cnf</i>, you will notice 2 lines that relate to slow queries, make sure you uncomment them and restart MySQL. The long_query_time can be adjusted to say 10 seconds, so that any query running longer than 10 seconds is logged. See <a href="https://dev.mysql.com/doc/refman/5.1/en/slow-query-log.html" rel="nofollow">https://dev.mysql.com/doc/refman/5.1/en/slow-query-log.html</a> for additional details.</p>
<pre class="brush: plain; title: ; notranslate" title="">
#log_slow_queries = /var/log/mysql/mysql-slow.log
#long_query_time = 2
</pre>
<p>Another thing you can do is enable logging for queries that are not using indexes. As mentioned above using indexes speeds up performance. In <i>/etc/mysql/my.cnf</i> uncomment the below line and restart MySQL. The log will be in the same place as mysql-slow.log.</p>
<pre class="brush: plain; title: ; notranslate" title="">
#log-queries-not-using-indexes
</pre>
<p>Query cache is another item to check. MySQL caches queries and returns results from this cache if the table has not changed. This has a performance improvement of over 200%. You should check the query cache to ensure that there is no memory for the cache and that the cache is not having to be cleared for new items. Additional information can be found here <a href="https://dev.mysql.com/doc/refman/5.1/en/query-cache.html" rel="nofollow">https://dev.mysql.com/doc/refman/5.1/en/query-cache.html</a>.</p>
{% highlight bash %}
mysql> SHOW VARIABLES LIKE 'have_query_cache';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| have_query_cache | YES   |
+------------------+-------+
1 row in set (0.00 sec)

mysql> SHOW STATUS LIKE 'Qcache%';
+-------------------------+----------+
| Variable_name           | Value    |
+-------------------------+----------+
| Qcache_free_blocks      | 12       |
| Qcache_free_memory      | 16491184 |
| Qcache_hits             | 7645     |
| Qcache_inserts          | 5539     |
| Qcache_lowmem_prunes    | 0        |
| Qcache_not_cached       | 277      |
| Qcache_queries_in_cache | 156      |
| Qcache_total_blocks     | 334      |
+-------------------------+----------+
8 rows in set (0.00 sec)
{% endhighlight %}
<hr />
<h3>Linux Systems</h3>
<h5>Define the boot process of a Linux system</h5>
<ul>
<li> Once you power a system on, the first thing that happens is the BIOS loads and performs POST or a power on self test, to ensure that the components needed for a boot are ok. For instance if the CPU is defective, the system will give an error that POST has failed. (BIOS stands for Basic Input/Output system)</li>
<li>After POST the BIOS looks at the MBR or master book record and executes the boot loader. In case of a Linux system that might be GRUB or Grand Unified BootLoader. GRUB&#8217;s job is to give you the choice of loading a Linux kernel or other OS that you may be running</li>
<li>Once you ask GRUB to load a kernel, usually an initial ramdisk kernel is loaded, which is a small kernel that understands filesystem. This will in turn mount the filesystem and will start the Linux kernel from the filesystem</li>
<li>The kernel will then start init, which is the very first process, usually having PID 1. Init will look at /etc/inittab and will switch to the default run-level which on Linux servers tends to be 3.</li>
<li>There are different run level scripts in /etc/rc.d/rc[0-6].d/ which are then executed based on the runlevel the system needs to be in.</li>
<li>And that&#8217;s about it!</li>
</ul>
<h5>How do you make changes to kernel parameters that you want to persist across boot?</h5>
<p>/etc/sysctl.conf contains kernel parameters that can be modified. You can also use the sysctl command to make changes at runtime.</p>
<hr />
<h3>Security</h3>
<h5>How does SSL work?</h5>
<p>SSL stands for secure socket layer. It has been superseded by TLS or transport layer security. TLS is a secure way of communicating through a network. A majority of secure HTTP communication on the web takes place using TLS.  TLS works at session layer and presentation layer of the OSI model.  Initially at the session layer asymmetric encryption takes place, after that at the presentation later symmetric cipher and session key are used. The basic principle behind TLS is to encrypt data going across the network using public key encryption first, followed by using a shared key. Also the other component of TLS is server certificate authentication which is done through a certificate authority. Clients contain a list of certificate authorities, and it uses the public key of the CA in the certificate to verify the certificate being authentic.  A good reference for TLS is here <a href="https://en.wikipedia.org/wiki/Secure_Socket_Layer" rel="nofollow">https://en.wikipedia.org/wiki/Secure_Socket_Layer</a>.</p>
<hr />
<h3>Miscellaneous</h3>
<h5>How does Apache worker.c compare to prefork?</h5>
<p>Worker.c uses threads. Prefork uses forks. Prefork is by default in Apache. Worker.c uses less resources, but is more complex. </p>
## How do you update packages on Fedora/Ubuntu?

###Ubuntu
{% highlight bash %}
sudo apt-get update -y
{% endhighlight %}

###Fedora
{% highlight bash %}
sudo yum update -y
{% endhighlight %}
##How do you upgrade to a new release on Fedora/Ubuntu?

###Ubuntu
{% highlight bash %}
sudo apt-get upgrade -y
{% endhighlight %}

###Fedora
Use FedUP <https://fedoraproject.org/wiki/FedUp>

## How do you use SSH proxy to connect to a remote host?
In your $HOME/.ssh/config use:
{% highlight bash %}
TCPKeepAlive=yes
ServerAliveInterval=15

Host finaldestinationhost
   ProxyCommand ssh user@jumphost nc finaldestinationhost %p
{% endhighlight %}
To ssh use ssh user@finaldestinationhost.
## How do you use SSH to create a dynamic tunnel?
Let's say there are 3 hosts, one is source, the other is destination and you cannot get to the destination from the source.

In the middle is a gateway that can reach both the source and the destination.

One possible solution to get from source to destination using SSH dynamic tunnel, is to create a dynamic tunnel.

The way it would work is.
## How do you use change MySQL root password?
{% highlight bash %}
$mysql -u root -p
>use mysql;
>update user set password=PASSWORD("NEWPASSWORD") where User='root';
>flush privileges;
{% endhighlight %}