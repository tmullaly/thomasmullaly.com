---
layout: post
title:  "Ubuntu AD Client"
date: 2012-09-16
categories: ["ubuntu", "linux"]
---
This is my original way of using Active Directory as the directory server for linux.  It requires the schema to be extended to include the unix attributes.  In Windows Server 2008 AD, the schema is actually extended by default.  Unfortunately, in order to access the unix attributes in the AD MMC you need to add the NIS role.  The new way of auto generating the userid is way better and is documented here: [[ubuntu rid client]]

{% highlight bash %}
sudo apt-get install krb5-user libpam-krb5 libpam-ccreds auth-client-config
{% endhighlight %}

edit /etc/krb5.onf
{% highlight text %}
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

sudo apt-get install samba winbind
{% highlight text %}
  #GLOBAL PARAMETERS
  [global]
     workgroup = CS
     realm = CS.UMB.EDU
     preferred master = no
     server string = %h Linux Test Machine
     security = ADS
     encrypt passwords = yes
     log level = 3
     log file = /var/log/samba/%m
     max log size = 50
     printcap name = cups
     printing = cups
     winbind enum users = No
     winbind enum groups = No
     winbind use default domain = Yes
     winbind nested groups = Yes
     winbind separator = +
     winbind nss info = rfc2307
     winbind cache time = 10
     idmap backend = tdb
     idmap uid = 60000-70000
     idmap gid = 60000-70000
     idmap config CS: backend = ad
     idmap config CS: range = 10000-20000
     idmap config CS: schema_mode = rfc2307
     ;template primary group = "Domain Users"
     ;template shell = /bin/bash
  
  [homes]
     comment = Home Direcotries
     valid users = %S
     read only = No
     browseable = No
  
  [printers]
     comment = All Printers
     path = /var/spool/cups
     browseable = no
     printable = yes
     guest ok = yes
{% endhighlight %}

edit /etc/nsswitch.conf
{% highlight text %}
passwd:         compat winbind
group:          compat winbind
{% endhighlight %}

net ads join -U Administrator

wbinfo -u
