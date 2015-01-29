---
layout: post
title: "Request Tracker 4.2.9 install on Ubuntu 14.04"
date: 2014-11-28 18:19:52 -0500
comments: true
categories: ["information-technology", "linux", "ubuntu", "perl"]
---
I started a new job last week and one of the systems they don't have is a ticket request tracker.  I've come to be dependent on Request Tracker (RT) from my time at Northeastern University and at UMass Boston.  At Orange, we managed our tasks with a share point list and an email alias, which worked fine, but looking back, I should have implemented RT.
<!-- more -->

If you're not familiar with RT, the first release in 1996 and was written by Jesse Vincent.  RT is an issue ticket request tracker which began with an email interface and a tcl/tk interface.  Now there is a web interface.  The web interface isn't the best, but the latest version (4.2) is much better looking than the releases of the past.  I wish they would just twitter bootstrap the who thing and not bother or worry about the css.

The web interface is really for the help desk, not the ticket requestors.  The ticket requests will continue to be primariy entered by sending email to the system.

I'm building this as a KVM Domain so let's get started and clone the Ubuntu 14.04 gold master.  Again, I realize I should be doing this in a devops-y way, but the install still needs to be documented before I can automate it.  Also, I don't want to use the package manager to install RT becasue I want the latest version.

{% highlight bash %}
virt-clone --connect=qemu:///system -o UbuntuServer1404 -n RT -f RT.qcow2
{% endhighlight %}

Login to the system when it comes up, become root with "sudo -i" and edit the following files to change the hostname to rt:
{% highlight bash %}
vi /etc/hostname
vi /etc/hosts
{% endhighlight %}

Now reboot, login again, become root and update the system and reboot:
{% highlight bash %}
sudo -i
apt-get update
apt-get upgrade
reboot
{% endhighlight %}

Login again, become root and cd into /usr/local/src.  Download the latest version of RT from the author's website, Best Practical.  Gunzip and extract the tar file, cd into the newly created directory.
{% highlight bash %}
cd /usr/local/src
wget https://download.bestpractical.com/pub/rt/release/rt.tar.gz
gunzip rt.tar.gz
tar -xvf rt.tar
{% endhighlight %}

I've already gone through and figured out the package dependancies:
{% highlight bash %}
apt-get install build-essential mysql-server apache2 postfix mailutils openssl libyaml libyaml-appconfig-perl openssl-dev libexpat1-dev
{% endhighlight %}
Now let's see what's missing:
{% highlight bash %}
./configure
make testdeps
{% endhighlight %}
Configure CPAN
{% highlight bash %}
/usr/bin/perl -MCPAN -e shell
{% endhighlight %}
'make fixdeps' will install all the CPAN modules:
{% highlight bash %}
make fixdeps
{% endhighlight %}
Test again to see that all dependancies have been met:
{% highlight bash %}
make testdeps
{% endhighlight %}
Once you have met the dependancies you can install RT:
{% highlight bash %}
make install
{% endhighlight %}
Then
{% highlight bash %}
make initialize-database
{% endhighlight %}
Now comes the tricky part.  First add mod_fastcgi:
{% highlight bash %}
apt-get install libapache2-mod-fastcgi
a2enmod fastcgi
apache2ctl graceful
{% endhighlight %}
Create the Request Tracker apache conf, you can compy and paste this into your terminal, hit ctrl-d to save.  This is the trick, the example on the RT website is incorrect, notice I've commented out Order and Allow and added the Require statement.
{% highlight bash %}
cat > /etc/apache2/sites-available/request-tracker.conf
# Tell FastCGI to put its temporary files somewhere sane; this may
    # be necessary if your distribution doesn't already set it
    #FastCgiIpcDir /tmp

    FastCgiServer /opt/rt4/sbin/rt-server.fcgi -processes 5 -idle-timeout 300

    <VirtualHost rt.thunderhouse.com>
        ### Optional apache logs for RT
        # Ensure that your log rotation scripts know about these files
        # ErrorLog /opt/rt4/var/log/apache2.error
        # TransferLog /opt/rt4/var/log/apache2.access
        # LogLevel debug

        AddDefaultCharset UTF-8

        ScriptAlias / /opt/rt4/sbin/rt-server.fcgi/

        DocumentRoot "/opt/rt4/share/html"
        <Location />
            #Order allow,deny
            #Allow from all
	        Require all granted

            Options +ExecCGI
            AddHandler fastcgi-script fcgi
        </Location>
    </VirtualHost>
{% endhighlight %}
enable it:
{% highlight bash %}
a2ensite request-tracker.conf
{% endhighlight %}
restart apache
{% highlight bash %}
apachectl restart
{% endhighlight %}
Hit the server with a browser and you should be on your way.  The default account is root/password.




