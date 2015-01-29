---
layout: post
title:  "CentOS 7 How to disable the User List"
date:   2014-07-22
categories: linux
---
I am starting a new virtualization project using kvm so as my host I wanted to use RedHat as the host operating system.

I downloaded CentOS 7.0 and installed it, it's very slick.  I installed off of the live disk, so I have a full desktop environment.


Here's the first thing I've changed, I didn't add a user, I'm logging-in as root and I wanted to get rid of the "not listed?" label and just have a text box to type the username in.

<!-- more -->

Create this file
{% highlight bash %}
vi /etc/dconf/db/gdm.d/00-login-screen
{% endhighlight %}
Add this content to that file:
{% highlight bash %}
[org/gnome/login-screen]
# Do not show the user list
disable-user-list=true
{% endhighlight %}
Run this command and restart X, I just rebooted
{% highlight bash %}
dconf update
{% endhighlight %}