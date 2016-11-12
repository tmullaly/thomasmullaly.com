---
layout: post
title: "Pure-FTPd on Ubuntu 12.04"
date: 2015-04-22 20:37:07 -0400
comments: true
categories: ["linux", "ubuntu"]
---
This is a simple setup to allow your designer ftp access to a web directory.  The problem that I encountered was for a web server running wordpress.  Apache runs as the user www-data with an id of 33 on Ubuntu 12.04.  The wordpress files have to be ownership as the webserver for you to download and install updates through wordpress's web interface.  I guess I could just have the core wordpress files and the plugins directory and given the rest the user account permissions but I didn't.  The Pure-ftp server allow me to create virtual users and I can give them any uid, except 0 (root).  For security, I would just allow from the designers ip address.
<!-- more -->
{% highlight bash %}
apt-get install pure-ftpd
{% endhighlight %}
{% highlight bash %}
cd /etc/pure-ftpd/conf
{% endhighlight %}
{% highlight bash %}
echo "41000 42000" > PassivePortRange
echo "yes" > DontResolve
echo "33" > MinUID
{% endhighlight %}
{% highlight bash %}
pure-pw useradd someuser -u www-data -d /var/www
pure-pw mkdb
{% endhighlight %}
{% highlight bash %}
cd /etc/pure-ftpd/auth
ln -s ../conf/PureDB 50pure
{% endhighlight %}
{% highlight bash %}
/etc/init.d/purftpd restart
{% endhighlight %}
{% highlight bash %}
iptables -A INPUT -s $ip -p udp --dport 21 -j ACCEPT
iptables -A INPUT -s $ip -p tcp --dport 21 -j ACCEPT
iptables -A INPUT -s $ip -p udp --dport 41000:42000 -j ACCEPT
iptables -A INPUT -s $ip -p tcp --dport 41000:42000 -j ACCEPT
{% endhighlight %}