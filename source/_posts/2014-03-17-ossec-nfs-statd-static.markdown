---
layout: post
title:  "Ossec NFS statd static port"
date:   2014-03-17 18:08:55
categories: security
---
First edit /etc/default/nfs-common and add:

{% highlight bash %}
STATDOPTS="--port 4000 --outgoing-port 4001"
{% endhighlight %}

Next add the file /etc/modprobe.d/options.conf

{% highlight bash %}
options lockd nlm_udpport=4002 nlm_tcpport=4002
{% endhighlight %}

reboot and you should be good