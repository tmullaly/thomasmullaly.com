---
layout: post
title:  "Oracle Solaris 11"
date:   2013-03-10
categories: solaris
---
Oracle has released solaris 11.  It's great, but they have completely changed everything.  Checkout the official docs here: http://www.oracle.com/technetwork/server-storage/solaris11/documentation/index.html 

Here's my notes:

#Network Time Protocol
Since I don't think there's a vmware tools or open vm tools for solaris I'm putting ntp on.
{% highlight bash %}
cd /etc/inet
cp ntp.client ntp.conf
svcadm enable ntp
svcs | grep ntp
{% endhighlight %}
#Kerberos
Make sure you've entered the hostname into DNS

Login in as root and type
{% highlight bash %}
kinit Administrator@TOM.UMB.EDU
{% endhighlight %}
You should see something like this in return
{% highlight bash %}
Password for Administrator@TOM.UMB.EDU:
kinit: no ktkt_warnd warning possible
{% endhighlight %}
Type klist
{% highlight bash %}
Ticket cache: FILE:/tmp/krb5cc_0
Default principal: Administrator@TOM.UMB.EDU

Valid starting                     Expires                              Service principle
08/12/2012 21:11    09/12/2012 07:13 krbtgt/TOM.UMB.EDU@TOM.UMB.EDU
        renew until 15/12/2012 21:11
{% endhighlight %}

#Samba and Winbind
{% highlight bash %}
pkg install samba
{% endhighlight %}
Copy the good smb config to /etc/samba

join the domain

{% highlight bash %}
net ads join -U Administrator@TOM.UMB.EDU
{% endhighlight %}
start samba and winbind
{% highlight bash %}
svcadm enable samba
svcadm enable winbind
{% endhighlight %}
test
{% highlight bash %}
  wbinfo -u
{% endhighlight %}
edit nsswitch.conf

test
{% highlight bash %}
  getent passwd
{% endhighlight %}

#Setup PAM and nsswitch

Here we are going to enable the use of winbind through PAM. This will give us the capability of using active directory users and groups when assigning permissions on files and directories. We could also use this for other authentication methods that use PAM such as apache web sites (but that is another discussion).

Enable winbind in pam.conf
{% highlight bash %}
cp /etc/pam.conf /etc/pam.conf-OLD
cp /etc/pam.conf-winbind /etc/pam.conf
{% endhighlight %}
Enable winbind in nsswitch.conf to set up the search order for all authentication. The system will now look at the local files followed by a check against winbind.
{% highlight bash %}
svccfg -s name-service/switch 
> setprop config/password = "files winbind"
> setprop config/group = "files winbind"
> exit
svcadm refresh name-service/switch
{% endhighlight %}
Make sure the entries were adjusted in nsswitch by:

cat /etc/nsswitch.conf
You should see the following entries in passwd and group:
{% highlight bash %}
passwd:     files winbind
group:      files winbind
{% endhighlight %}

#Add OpenCSW to software package repositories
To install third party software from a package repository:
{% highlight bash %}
pkgadd -d https://get.opencsw.org/now
{% endhighlight %}
Add /opt/csw/bin to your path
{% highlight bash %}
export PATH=$PATH:/opt/csw/bin
{% endhighlight %}
You can now install with the pkgutil command:
{% highlight bash %}
pkgutil --install puppet
{% endhighlight %}

#Services
List all services
{% highlight bash %}
scvs
{% endhighlight %}
  
Start/Stop/Restart/Enable/Disable a service
{% highlight bash %}
svcadm <cmd> <service>
{% endhighlight %}

#Setup domainname
{% highlight bash %}
  svcadm disable nis/domain
  svccfg -s svc:/network/nis/domain listprop config/domainname
  svccfg -s svc:/network/nis/domain setprop config/domainname = <your domain name>
  svccfg -s nis/domain:default refresh
  svcadm enable nis/domain
{% endhighlight %}
Verify that domainname is setup correctly
{% highlight bash %}
domainname
{% endhighlight %}

#Installing kadmin
If you find youself thinking Kerberosis installed, but I could not find kadmin. What package is it in?

Only the basic packages
are installed by default and the KDC is not one of them. If you want a
package/command that isn't included in the base install you can search
for it like this:
{% highlight bash %}
$ pkg search -r kadmin
{% endhighlight %}