---
layout: post
title: "Collectd on Ubuntu 12.04 and Nginx"
date: 2015-04-13 20:55:44 -0400
comments: true
categories: ["collectd","linux","ubuntu"]
---
Collectd is the new cool kid of system monitoring tools.

{% highlight bash %}
apt-get install collectd collectd-utils
{% endhighlight %}

{% highlight bash %}
vi /etc/collectd/collectd.conf
{% endhighlight %}

{% highlight bash %}
service collectd restart
{% endhighlight %}

{% highlight bash %}
/var/ossec/bin/syscheck_update -u local
{% endhighlight %}

{% highlight bash %}
cd /var
mkdir www
cd www
cp -r /usr/share/doc/collectd/examples/collection3 .
{% endhighlight %}

{% highlight bash %}
apt-get install librrds-perl libconfig-general-perl libhtml-parser-perl  libregexp-common-perl
apt-get install fast-cgi
apt-get install spawn-fcgi fcgiwrap
{% endhighlight %}

{% highlight bash %}
        location /nginx_status {
          stub_status on;
          access_log off;
          allow 127.0.0.1;
          deny all;
        }
		
		location ~ .cgi$ {
	        	root /var/www;
	        	fastcgi_pass unix:/var/run/fcgiwrap.socket;
	        	include /etc/nginx/fastcgi_params;
	    	}

		location /collection3/share/ {
			alias /var/www/collection3/share/;
		}
{% endhighlight %}

{% highlight bash %}
service nginx restart
{% endhighlight %}
