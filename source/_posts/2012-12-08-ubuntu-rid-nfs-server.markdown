---
layout: post
title:  "Ubuntu RID NFS Server"
date:   2012-12-08
categories: ["linux", "capstone"]
---
##Network Configuration	
rename to fileserver:
{% highlight bash %}
vi /etc/hostname
vi /etc/hosts
{% endhighlight %}

Create static interface and add the Active Directory Server as the DNS
{% highlight bash %}
vi /etc/network/interfaces
{% endhighlight %}

{% highlight bash %}
auto eth0
iface eth0 inet static
  address 10.10.10.30
  netmask 255.255.255.0
  network 10.10.10.0
  gateway 10.10.10.1
  dns-nameservers 10.10.10.10
  dns-search tom.edu
{% endhighlight %}

reboot and check if /etc/resolve.conf has the windows ad server as its dns

##Get Kerberos working
{% highlight bash %}
  sudo apt-get install krb5-user libpam-krb5 libpam-ccreds auth-client-config
{% endhighlight %}

use TOM.EDU as Realm
{% highlight bash %}
  vi /etc/krb5.conf
{% endhighlight %}

{% highlight bash %}
  [logging]
  default = FILE:/var/log/krb5libs.log
  kdc = FILE:/var/log/krb5kdc.log
  admin_server = FILE:/var/log/kadmind.log
  
  [libdefaults]
  dns_lookup_realm = true
  dns_lookup_kdc = true
  ticket_lifetime = 24h
  forwardable = yes
  
  [appdefaults]
  pam = {
   debug = false
   ticket_lifetime = 36000
   renew_lifetime = 36000
   forwardable = true
   krb4_convert = false
  }
{% endhighlight %}

test kerberos
{% highlight bash %}
[root@rid-nfs ~]# host -t srv _kerberos._tcp.tom.edu
_kerberos._tcp.tom.edu has SRV record 0 100 88 ad.tom.edu.
{% endhighlight %}
  
##Samba and winbind
{% highlight bash %}
sudo apt-get install samba winbind
{% endhighlight %}

{% highlight bash %}
vi /etc/samba/smb.conf
{% endhighlight %}

{% highlight bash %}
  #GLOBAL PARAMETERS
  [global]
     workgroup = TOM
     realm = TOM.EDU
     preferred master = no
     server string = Home Directory Server
     security = ADS
     encrypt passwords = yes
     log level = 3
     log file = /var/log/samba/%m
     max log size = 50
     printcap name = cups
     printing = cups
     winbind enum users = Yes
     winbind enum groups = Yes
     winbind use default domain = Yes
     winbind nested groups = Yes
     winbind separator = +
     winbind cache time = 10
     idmap uid = 10000-20000
     idmap gid = 10000-20000
     idmap config TOM: backend = rid
     idmap config TOM: range = 10000-20000
     allow trusted domains = No
     template shell = /bin/bash
     template homedir = /home/%D/%U
     ;template primary group = "Domain Users"
  
  [homes]
     comment = Home Direcotries
     path = /export/home/
     valid users = %S
     read only = No
     browseable = No
     inherit acls = yes
     inherit permissions = yes
     writable = yes
  
  [printers]
     comment = All Printers
     path = /var/spool/cups
     browseable = no
     printable = yes
     guest ok = yes
{% endhighlight %}

{% highlight bash %}
vi nsswitch.conf
{% endhighlight %}

{% highlight bash %}
passwd: compat winbind
group: compat winbind
shadow: compat
{% endhighlight %}

restart winbind nmbd and smbd

join the domain
{% highlight bash %}
net ads join -U Administrator
{% endhighlight %}

test:
{% highlight bash %}
wbinfo -u
getent passwd
{% endhighlight %}

##Setup the NFS Server
{% highlight bash %}
apt-get install acl nfs-kernel-server
{% endhighlight %}

{% highlight bash %}
mkdir /export/home
{% endhighlight %}

add user directories and modify permissions
{% highlight bash %}
vi /etc/exports
{% endhighlight %}

{% highlight bash %}  
/export/home 10.10.10.0/24(rw,sync,no_subtree_check)
{% endhighlight %}

export the new directory
{% highlight bash %}
service nfs-kernel-server restart
{% endhighlight %}

test:
{% highlight bash %}
exportfs
{% endhighlight %}  

##Enable ACL support (important, this will fix file permission creation issues)

Open /etc/fstab, navigate to the entry for the home directory partition, and add acl to the list of options.
{% highlight bash %}
/dev/sda1 /export/home ext3 defaults,acl 0 0
{% endhighlight %}
With this option in place, remount the ﬁlesystem.
{% highlight bash %}
sudo mount -o remount /dev/sda1
{% endhighlight %}
  
My setup was a bit different in /etc/fstab
{% highlight bash %}
/dev/mapper/ubuntu-root /               ext4    errors=remount-ro,acl 0       1
{% endhighlight %}
##Sign the Puppet Cert on Master
{% highlight bash %}
root@puppet:/etc/puppet# puppet cert list
  "fileserver.tom.edu" (MD5) E0:C3:90:FF:55:A8:81:A3:05:DD:61:A4:07:4B:EB:7B
root@puppet:/etc/puppet# puppet cert sign fileserver.tom.edu
Signed certificate request for fileserver.tom.umb.edu
Removing file Puppet::SSL::CertificateRequest fileserver.tom.edu at '/var/lib/puppet/ssl/ca/requests/fileserver.tom.edu.pem'
{% endhighlight %}

##Go Setup the Linux Client
[ubuntu rid client]({% post_url 2013-03-09-ubuntu-rid-client %})