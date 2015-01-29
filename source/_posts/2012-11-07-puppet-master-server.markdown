---
layout: post
title:  "Puppet Master Server"
date:   2012-11-07
categories: puppet
---
##Choose a Package Source
Download the “puppetlabs-release” package for your OS version.
You can see a full list of these packages on the front page of http://apt.puppetlabs.com/. 
They are all named puppetlabs-release-(CODE NAME).deb.

Install the package by running dpkg -i <PACKAGE NAME>.
For example, to enable the repository for Ubuntu 12.04 Precise Pangolin:
<!-- more -->
{% highlight bash %}
$ wget http://apt.puppetlabs.com/puppetlabs-release-precise.deb
$ sudo dpkg -i puppetlabs-release-precise.deb
$ sudo apt-get update
{% endhighlight %}
  
##Install the Puppet Master
{% highlight bash %}
apt-get install puppetmaster
{% endhighlight %}

##Configure Reporting
How to see client changes in the master log
My default puppet configuration shows all the interesting changes in the client's /var/log/daemon.log. I'd rather be able to see those client-side changes in the master's log. Making this happen involves turning on puppet's client reporting mechanism.

###client-side reporting
This is controlled by a setting in puppet.conf or a command line parameter to puppetd. I added the switch to 
/etc/default/puppet:
{% highlight bash %}
# Defaults for puppet - sourced by /etc/init.d/puppet

# Start puppet on boot?
START=yes

# Startup options
DAEMON_OPTS="--report"
{% endhighlight %}

###server handling of reports
On the server side, puppet has several options for processing reports. The default is to create a yaml file in /var/lib/puppet/report/<nodename> To tell puppet to send the client report of changes to syslog (which in this case is sent to daemon.log), make this change to /etc/puppet/puppet.conf
{% highlight bash %}
[master]
reports = log
{% endhighlight %}
Now, if you fiddle with a puppet controlled resource on the client (like adding a blank line to /etc/hosts), and restart the puppet client, you'll see puppet's corrective actions reported in the master's /var/log/daemon.log.

##Signing Client Certificates
On the master:
{% highlight bash %}
puppet cert list
puppet cert sign blue.cs.umb.edu
{% endhighlight %}