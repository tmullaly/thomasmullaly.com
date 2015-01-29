---
layout: post
title: "Apache VCL install on CentOS 6.5"
date: 2014-10-10 15:23:53 -0400
comments: true
categories: ["centos", "linux", "virtualization", "kvm", "apache"]
---
I've been meaning to install Apache's VCL (Virtual Computer Lab) in my new kvm environment.  I'm looking to get a job in a college or university and this solution works incredibly well.  How do I know?  Well, becasue I recently went back to finish my undergraduate degree, I used all these new systems as a student and the VCL was one of them.
<!-- more -->
I don't have my provisioning system up and running as of yet so we're gonna do this by hand:
{% highlight bash %}
root@kvm:/var/lib/libvirt/images# virt-install -n ApacheVCL -r 1024 /
--disk path=/var/lib/libvirt/images/ApacheVCL.qcow2,bus=virtio,size=8,format=qcow2 -c /
 /var/lib/libvirt/images/CentOS-6.5-x86_64-minimal.iso --accelerate /
 --network network=default,model=virtio --connect=qemu:///system --vnc /
 --noautoconsole -v --os-type=linux --os-variant=rhel6
{% endhighlight %}
Perform the minimal install, boot it, connect to the console using vnc, login as root, edit /etc/sysconfig/network-scripts/ifcfg-eth0 and change ONBOOT=no to ONBOOT=yes, now reboot.  From here I'm going to ssh to my kvm host and then ssh to the ApacheVCL guest, so when the CentOS guest comes back up login on the console one more time and get the ip address, ifconfig should work.  (As a side note, in CentOS 7, RedHat decided that ifconfig is obsolete and removed it from the minimal install, the new command is "ip").

Now do a yum update, reboot and ssh back in.

Go get the ApacheVCL tarball:
{% highlight bash %}
curl -O http://mirrors.gigenet.com/apache/vcl/apache-VCL-2.3.2.tar.bz2
{% endhighlight %}
Extract the files:
{% highlight bash %}
tar -jxvf apache-VCL-2.3.2.tar.bz2
{% endhighlight %}
Install MySQL:
{% highlight bash %}
yum install mysql-server -y
{% endhighlight %}
(note here, if you are trying this on CentOS 7, mysql-server has been replaced with mariadb-server)

Now configure the MySQL daemon (mysqld) to start automatically:
{% highlight bash %}
/sbin/chkconfig --level 345 mysqld on
{% endhighlight %}

Start the MySQL daemon:
{% highlight bash %}
/sbin/service mysqld start
{% endhighlight %}

Run the MySQL command-line client:
{% highlight bash %}
mysql
{% endhighlight %}

Create a database:
{% highlight bash %}
CREATE DATABASE vcl;
{% endhighlight %}

Create a user with SELECT, INSERT, UPDATE, DELETE, and CREATE TEMPORARY TABLES privileges on the database you just created:
{% highlight bash %}
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE TEMPORARY TABLES ON vcl.* TO 'vcluser'@'localhost' IDENTIFIED BY 'vcluserpassword';
{% endhighlight %}

Exit the MySQL command-line client
{% highlight bash %}
exit
{% endhighlight %}

Import the vcl.sql file into the database. The vcl.sql file is included in the mysql directory within the Apache VCL source code
{% highlight bash %}
mysql vcl < apache-VCL-2.3.2/mysql/vcl.sql
{% endhighlight %}

Install the required Linux packages and PHP modules:
{% highlight bash %}
yum install httpd mod_ssl php php-gd php-mysql php-xml php-xmlrpc php-ldap php-process -y
{% endhighlight %}

Configure the web server daemon (httpd) to start automatically:
{% highlight bash %}
/sbin/chkconfig --level 345 httpd on
{% endhighlight %}

Add hostname to /etc/hosts

Start the web server daemon
{% highlight bash %}
/sbin/service httpd start
{% endhighlight %}

Check if SELinux is enabled:
{% highlight bash %}
[root@vcl ~]# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /selinux
Current mode:                   enforcing
Mode from config file:          enforcing
Policy version:                 24
Policy from config file:        targeted
{% endhighlight %}

If SELinux is enabled, run the following command to allow the web server to connect to the database:
{% highlight bash %}
/usr/sbin/setsebool -P httpd_can_network_connect=1
{% endhighlight %}

Check IP Tables:
{% highlight bash %}
[root@vcl ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     all  --  anywhere             anywhere            state RELATED,ESTABLISHED 
ACCEPT     icmp --  anywhere             anywhere            
ACCEPT     all  --  anywhere             anywhere            
ACCEPT     tcp  --  anywhere             anywhere            state NEW tcp dpt:ssh 
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited 

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited 

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         
{% endhighlight %}

If the iptables firewall is being used, port 80 and 443 should be opened up in the iptables config file:
{% highlight bash %}
vi /etc/sysconfig/iptables
{% endhighlight %}

Add these rules:
{% highlight bash %}
-A INPUT -m state --state NEW -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -p tcp --dport 443 -j ACCEPT
{% endhighlight %}

Restart iptables
{% highlight bash %}
service iptables restart
{% endhighlight %}

Now let's work on the frontend web code.

Copy the web directory to a location under the web root of your web server and navigate to the destination .ht-inc subdirectory:
{% highlight bash %}
cp -r apache-VCL-2.3.2/web/ /var/www/html/vcl
cd /var/www/html/vcl/.ht-inc
{% endhighlight %}

Copy secrets-default.php to secrets.php:
{% highlight bash %}
cp secrets-default.php secrets.php
{% endhighlight %}

Edit the secrets.php file:
{% highlight bash %}
vi secrets.php
{% endhighlight %}

Set the following variables to match your database configuration:
{% highlight bash %}
$vclhost
$vcldb
$vclusername
$vclpassword
{% endhighlight %}
Create random passwords for the following variables:
{% highlight bash %}
$cryptkey
$pemkey
{% endhighlight %}
Save the secrets.php file

Run the genkeys.sh
{% highlight bash %}
./genkeys.sh
{% endhighlight %}

Copy conf-default.php to conf.php:
{% highlight bash %}
cp conf-default.php conf.php
{% endhighlight %}

Modify conf.php to match your site
{% highlight bash %}
vi conf.php
{% endhighlight %}
Modify every entry under "Things in this section must be modified". Descriptions and pointers for each value are included within conf.php.
COOKIEDOMAIN - set this to the domain name your web server is using or leave it blank if you are only accessing the web server by its IP address

Set the owner of the .ht-inc/maintenance directory to the web server user (normally 'apache'):
{% highlight bash %}
chown apache maintenance
{% endhighlight %}

Open the testsetup.php page in a web browser:

If you set up your site to be https://192.168.122.110/vcl/ open https://192.168.122.110/vcl/testsetup.php

Debug any issues reported by testsetup.php

Now Log In to the VCL Website

Open the index.php page in your browser (https://192.168.122.110/vcl/index.php)

* Select Local Account
* Username: admin
* Password: adminVc1passw0rd
* Set the admin user password:

* Click User Preferences
* Enter the current password: adminVc1passw0rd
* Enter a new password
* Click Submit Changes


That's it for this post, in the next post I'll finish off the installation by adding a management node to the database.  
