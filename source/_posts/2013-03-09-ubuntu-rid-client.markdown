---
layout: post
title:  "Ubuntu winbind RID client"
date:   2013-03-09
categories: linux
---
#Network Time Protocol
Keeping a computer system's clock accurate is critical for many things.  In setting up samba and winbind to connect to a windows domain you need kerberos working.  Kerberos is dependent on the clock accuracy of the clients and servers.

**Do not install ntpd on virtual machines!!!**
Virtual machines should have the client vm tools installed, which among other things, keeps the clock synced with the host machine.  You **should** have some sort of time synchronization installed on the host machine.
{% highlight bash %}
sudo apt-get install ntp
{% endhighlight %}

#Kerberos
{% highlight bash %}
sudo apt-get install krb5-user libpam-krb5 libpam-ccreds auth-client-config
{% endhighlight %}

use TOM.UMB.EDU as Realm

Edit /etc/krb5.conf
{% highlight bash %}
vi /etc/krb5.conf
{% endhighlight %}
Make it look like this:
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
[root@rid-nfs ~]# host -t srv _kerberos._tcp.cs.umb.edu
_kerberos._tcp.cs.umb.edu has SRV record 0 100 88 win-ad.cs.umb.edu.
{% endhighlight %}

#Samba and winbind
{% highlight bash %}
sudo apt-get install samba winbind

vi /etc/samba/smb.conf

  #GLOBAL PARAMETERS
  [global]
     workgroup = TOM
     realm = TOM.UMB.EDU
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

  vi nsswitch.conf
{% endhighlight %}
restart winbind nmbd and smbd

join the domain
  net ads join -U Administrator

test
  wbinfo -u
  wbinfo -g
  getent passwd
  
As root:
  kinit -V
  net ads info
  
  
#nsswitch.conf
Edit your /etc/nsswitch.conf file and add winbind
{% highlight bash %}
passwd:         compat winbind
group:          compat winbind
shadow:         compat
{% endhighlight %}
  
#Home Directories
You've got three choices here

The traditional unix network used a centralized fileserver/fileservers to store the user's home directory.  Upon logon to any system on the network, the user's home directory would be auto-mounted and appear local.  This worked very well in the 80's and early 90's but today we live in a different world.

##Traditional Unix Automount
###Configure Automount
{% highlight bash %}
sudo apt-get install autofs
{% endhighlight %}
To mount user home directories at login, we need to conﬁgure the automount service. Open
/etc/auto.master and add the following entry.
/home /etc/auto.home

Now create /etc/auto.home and add this line
{% highlight bash %}
* -fstype=nfs,nolock,nosuid rid-nfs.cs.umb.edu:/export/home/&
{% endhighlight %}
###Configure PAM
If you want to be able to change your password

/etc/pam.d/common-password:
{% highlight bash %}
password   sufficient pam_winbind.so
password   required   pam_unix.so nullok obscure min=4 max=8 md5
{% endhighlight %}

##The New Way, PAM Make Home Directory
The pam_mkhomedir PAM module will create a users home directory if it does not exist when the session begins. This allows users to be present in central database (such as NIS, kerberos or LDAP) without using a distributed file system or pre-creating a large number of directories.  The skeleton directory (usually /etc/skel/) is used to copy default files and also set’s a umask for the creation.  This is probably the best for performance reasons and local disks a big and cheap.  All software should be in source control anyway.

The new users home directory will not be removed after logout of the user.

Add this to /etc/pam.d/common-account
{% highlight bash %}
session    required    pam_mkhomedir.so skel=/etc/skel/ umask=0022
{% endhighlight %}
You can read more about it on the debian documentation site: http://www.debian-administration.org/articles/403

##smb auto mounting
Actually you can skip nfs altogether and mount the users windows homedirectory:

Install libpam-mount:

  apt-get install libpam-mount
  
When you install libpam-mount it should add the pammount directive in pam.d/common-session
  
I changed where the directory gets mounted to be just /home/username in smb.conf

Now edit the /etc/security/pam_mount.conf.xml
{% highlight bash %}
<!-- mkmountpoint enable="1" remove="true" / -->
<mkmountpoint enable="1" remove="false" />


<volume user="*" server="snap" path="home_dir" mountpoint="home" fstype="cifs" />

<cifsmount>mount -t cifs //%(SERVER)/%(USER) %(MNTPT)/%(USER) -o "user=%(USER),uid=%(USERUID),gid=%(USERGID)%(before=\",\" OPTIONS)"</cifsmount>

<umount>umount %(MNTPT)/%(USER)</umount>

</pam_mount>
{% endhighlight %}
