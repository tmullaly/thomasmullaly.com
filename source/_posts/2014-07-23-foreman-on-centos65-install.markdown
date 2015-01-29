---
layout: post
title:  "Foreman install on CentOS 6.5"
date:   2014-07-22
categories: puppet
---
##Host Setup
I created a new vm using KVM's Virtual Machine Manager GUI. 8 GB HD, 1024 MB RAM, 1 CPU.  Then I installed CentOS 6.5 minimal.  The system boots:

Edit /etc/sysconfig/network and add the GATEWAY

<!-- more -->

Now edit /etc/sysconfig/networking-scripts/ifcfg-eth0

{% highlight bash %}
DEVICE="eth0"
HWADDR=xx:xx:xx:xx:xx:xx
TYPE=Ethernet
UUID=5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
NAME="System eth0"
IPADDR=10.0.1.50
NETMASK=255.255.255.0
{% endhighlight %}

Now edit /etc/resolv.conf and add your nameservers

{% highlight bash %}
nameserver 8.8.8.8
nameserver 8.8.4.4
{% endhighlight %}

Make sure your hostname is in /etc/hosts.  put the fqdn in first.

I like to reboot to bring up the interface becasue it's a vm, it's fast and I know it will come up properly.

Log back in and yum update and yum upgrade, reboot.

##Enable EPEL Repository
{% highlight bash %}
[root@foreman ~]# yum install wget

[root@foreman ~]# yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.supremebytes.com
 * extras: mirrors.seas.harvard.edu
 * updates: mirror.net.cen.ct.gov
repo id              repo name                     status
base                 CentOS-6 - Base               6,367
extras               CentOS-6 - Extras             14
foreman              Foreman 1.5                   168
foreman-plugins      Foreman plugins 1.5           82
updates              CentOS-6 - Updates            1,213
repolist: 7,844



[root@foreman ~]# wget http://mirror.sfo12.us.leaseweb.net/epel/6/i386/epel-release-6-8.noarch.rpm
--2014-07-23 14:11:32--  http://mirror.sfo12.us.leaseweb.net/epel/6/i386/epel-release-6-8.noarch.rpm
Resolving mirror.sfo12.us.leaseweb.net... 108.59.10.97
Connecting to mirror.sfo12.us.leaseweb.net|108.59.10.97|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 14540 (14K) [application/x-redhat-package-manager]
Saving to: “epel-release-6-8.noarch.rpm”

100%[========================================================================================================================>] 14,540      --.-K/s   in 0.02s   

2014-07-23 14:11:32 (588 KB/s) - “epel-release-6-8.noarch.rpm” saved [14540/14540]

[root@foreman ~]# rpm -ivh epel-release-6-5.noarch.rpm --test
error: open of epel-release-6-5.noarch.rpm failed: No such file or directory
[root@foreman ~]# rpm -ivh epel-release-6-8.noarch.rpm --test
warning: epel-release-6-8.noarch.rpm: Header V3 RSA/SHA256 Signature, key ID 0608b895: NOKEY
Preparing...                ########################################### [100%]
[root@foreman ~]# wget https://fedoraproject.org/static/0608B895.txt
--2014-07-23 14:13:08--  https://fedoraproject.org/static/0608B895.txt
Resolving fedoraproject.org... 140.211.169.197, 152.19.134.146, 66.35.62.166, ...
Connecting to fedoraproject.org|140.211.169.197|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1715 (1.7K) [text/plain]
Saving to: “0608B895.txt”

100%[========================================================================================================================>] 1,715       --.-K/s   in 0s      

2014-07-23 14:13:08 (6.72 MB/s) - “0608B895.txt” saved [1715/1715]

[root@foreman ~]# mv 0608B895.txt /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
[root@foreman ~]# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
[root@foreman ~]# rpm -qa gpg*
gpg-pubkey-0608b895-4bd22942
gpgme-1.1.8-3.el6.x86_64
gpg-pubkey-c105b9de-4e0fd3a3
[root@foreman ~]# rpm -ivh epel-release-6-8.noarch.rpm --test
Preparing...                ########################################### [100%]
[root@foreman ~]# rpm -ivh epel-release-6-8.noarch.rpm
Preparing...                ########################################### [100%]
   1:epel-release           ########################################### [100%]
[root@foreman ~]# yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
epel/metalink                                                       |  13 kB     00:00     
 * base: mirror.supremebytes.com
 * epel: mirrors.mit.edu
 * extras: mirrors.seas.harvard.edu
 * updates: mirror.net.cen.ct.gov
epel                                                                | 4.4 kB     00:00     
epel/primary_db                                                     | 6.2 MB     00:15
repo id              repo name                                            status
base                 CentOS-6 - Base                                      6,367
epel                 Extra Packages for Enterprise Linux 6 - x86_64       11,029
extras               CentOS-6 - Extras                                    14
foreman              Foreman 1.5                                          168
foreman-plugins      Foreman plugins 1.5                                  82
updates              CentOS-6 - Updates                                   1,213
repolist: 18,873
[root@foreman ~]# 
{% endhighlight %}

##Add new rules to iptables
Let's list all the iptable rules with numbers
{% highlight bash %}
iptables -nL --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22 
5    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination         
1    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination       
{% endhighlight %}
Let's add http and https after ssh:
{% highlight bash %}
root@foreman ~]# iptables -I INPUT 5 -i eth0 -p tcp --dport 80 -j ACCEPT -m comment --comment "Foreman"
[root@foreman ~]# iptables -I INPUT 6 -i eth0 -p tcp --dport 443 -j ACCEPT -m comment --comment "Foreman"
[root@foreman ~]# iptables -nL --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22 
5    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:80 /* Foreman */ 
6    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:443 /* Foreman */ 
7    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination         
1    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination   
{% endhighlight %}
Let's save these rules  
{% highlight bash %}    
[root@foreman ~]# service iptables save
iptables: Saving firewall rules to /etc/sysconfig/iptables:[  OK  ]
{% endhighlight %}
If you screw up or whant to experiment you can restore iptable with this command:
{% highlight bash %}
service iptables restart
{% endhighlight %}

##Foreman Installation
The Foreman installer uses Puppet to install Foreman. This guide assumes that you have a newly installed operating system, on which the installer will setup Foreman, a puppet master with Passenger and the Smart Proxy by default.

{% highlight bash %}
yum -y install http://yum.theforeman.org/releases/1.5/el6/x86_64/foreman-release.rpm
yum -y install foreman-installer
{% endhighlight %}
{% highlight bash %}
[root@foreman ~]# foreman-installer
Installing             Done                                               [100%] [..............................................................................]
  Success!
  * Foreman is running at https://foreman.thunderhouse.com
      Default credentials are 'admin:changeme'
  * Foreman Proxy is running at https://foreman.thunderhouse.com:8443
  * Puppetmaster is running at port 8140
  The full log is at /var/log/foreman-installer/foreman-installer.log
[root@foreman ~]# 
{% endhighlight %}

##Install Compute Resource Types
If Ifrastructure -> Compute Resources -> New Compute Resource doesn't have any Types, do this:
{% highlight bash %}
yum install -y foreman-libvirt
service foreman restart
{% endhighlight %}
{% highlight bash %}
yum install -y foreman-ovirt
service foreman restart
{% endhighlight %}




