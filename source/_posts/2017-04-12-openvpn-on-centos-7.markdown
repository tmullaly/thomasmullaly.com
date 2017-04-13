---
layout: post
title: "OpenVPN on CentOS 7"
date: 2017-04-12 15:35:16 -0400
comments: true
categories: linux
---
Ubuntu is great for development but for production servers it requires constant patching.  I'm starting to use CentOS/RHEL more and more these days because I'm more concerned about security and stability and less about bleeding edge libraries.  Here's my instructions for setting up an OpenVPN server on CentOS 7.

Let's upgrade, remember that yum upgrade and yum update will perform the same function that update to the latest current version of package. But the difference is Upgrade will delete obsolete packages, while update will preserve them.
{% highlight bash %}
yum upgrade
reboot
{% endhighlight %}

{% highlight bash %}
# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1
{% endhighlight %}