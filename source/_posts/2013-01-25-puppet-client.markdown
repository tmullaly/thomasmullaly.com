---
layout: post
title:  "Puppet Client"
date:   2013-01-25
categories: linux
---
##Installing the puppet agent
These are my notes when I first started using puppet to manage 6 new linux desktops running Ubuntu 12.04
{% highlight bash %}
apt-get install puppet
{% endhighlight %}

Edit /etc/default/puppet
{% highlight bash %}
# Defaults for puppet - sourced by /etc/init.d/puppet

# Start puppet in boot?
START=yes

# Startup options
DAEMON_OPTS="--report"
{% endhighlight %}
##Add the puppet master host to DNS
In my case I'm adding to the local hosts file because I don't have access to DNS:
{% highlight bash %}  
23.22.145.26    puppet
{% endhighlight %}  
##Check for connectivity to the puppet master
{% highlight bash %}  
ping puppet
{% endhighlight %} 
##Agent Setup
{% highlight bash %}  
puppet agent --test
{% endhighlight %}
you should get this:
{% highlight bash %}  
root@red:~# puppet agent --test
warning: peer certificate won't be verified in this SSL session
warning: peer certificate won't be verified in this SSL session
info: Creating a new SSL certificate request for red.cs.umb.edu
info: Certificate Request fingerprint (md5): A0:A6:0C:8A:0E:A9:FD:AB:C9:5D:C8:3C:72:EE:33:57
warning: peer certificate won't be verified in this SSL session
warning: peer certificate won't be verified in this SSL session
warning: peer certificate won't be verified in this SSL session
Exiting; no certificate found and waitforcert is disabled
{% endhighlight %}
I got this because I reinstall the host and didn't backup the certs.
{% highlight bash %}  
root@red:~# puppet agent --test
info: Creating a new SSL key for red.cs.umb.edu
warning: peer certificate won't be verified in this SSL session
info: Caching certificate for ca
warning: peer certificate won't be verified in this SSL session
info: Caching certificate for red.cs.umb.edu
err: Could not request certificate: The certificate retrieved from the master does not match the agent's private key.
Certificate fingerprint: 4F:6B:D2:D7:A9:A4:DE:D6:59:AA:94:C8:59:26:3F:F5
{% endhighlight %}
To fix this, remove the certificate from both the master and the agent and then start a puppet run, which will automatically regenerate a certficate.
On the master:
{% highlight bash %}
puppet cert clean red.cs.umb.edu
{% endhighlight %}
On the agent:
{% highlight bash %}
rm -f /var/lib/puppet/ssl/certs/red.cs.umb.edu.pem
puppet agent -t
Exiting; failed to retrieve certificate and waitforcert is disabled
{% endhighlight %}

The Puppet agent found the puppet master, but it got stopped at the certificate roadblock. It isn’t authorized to fetch configurations, so the master is turning it away.

##Sign the cert on the puppet master
###List the certs waiting to be signed
  puppet cert list
  
{% highlight bash %}  
root@puppet:~# puppet cert list 
  "blu"            (MD5) 8B:96:E5:59:45:97:EF:C1:BA:34:0F:1E:91:62:AF:1C
  "red.cs.umb.edu" (MD5) A0:5D:C8:3C:72:EE:A6:0C:8A:0E:A4:FD:AB:C9:33:57
{% endhighlight %}
###Sign the cert
  puppet cert sign red.cs.umb.edu
{% highlight bash %}  
root@puppet:~# puppet cert sign red.cs.umb.edu
Signed certificate request for red.cs.umb.edu
Removing file Puppet::SSL::CertificateRequest red.cs.umb.edu at '/var/lib/puppet/ssl/ca/requests/red.cs.umb.edu.pem'
{% endhighlight %}
###Test the Client Agent again
puppet agent --test  
{% highlight bash %}  
root@red:~# puppet agent --test
warning: peer certificate won't be verified in this SSL session
info: Caching certificate for red.cs.umb.edu
info: Caching certificate_revocation_list for ca
info: Caching catalog for red.cs.umb.edu
info: Applying configuration version '1357769924'
{% endhighlight %}