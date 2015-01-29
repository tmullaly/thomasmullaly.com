---
layout: post
title: "Ubuntu Automatic Updates"
date: 2012-09-16
comments: true
categories: ["linux", "ubuntu"]
---
The unattended-upgrades package can be used to automatically install updated packages, and can be configured to update all packages or just install security updates. First, install the package by entering the following in a terminal:

{% highlight bash %}
sudo apt-get install unattended-upgrades
{% endhighlight %}
To configure unattended-upgrades, edit /etc/apt/apt.conf.d/50unattended-upgrades and adjust the following to fit your needs:

{% highlight bash %}
Unattended-Upgrade::Allowed-Origins {
	"${distro_id}:${distro_codename}-security";
//	"${distro_id}:${distro_codename}-updates";
//	"${distro_id}:${distro_codename}-proposed";
//	"${distro_id}:${distro_codename}-backports";
};
{% endhighlight %}
Certain packages can also be blacklisted and therefore will not be automatically updated. To blacklist a package, add it to the list:
{% highlight bash %}
Unattended-Upgrade::Package-Blacklist {
//      "vim";
//      "libc6";
//      "libc6-dev";
//      "libc6-i686";
};
{% endhighlight %}
The double “//” serve as comments, so whatever follows "//" will not be evaluated.

To enable automatic updates, edit /etc/apt/apt.conf.d/10periodic and set the appropriate apt configuration options:
{% highlight bash %}
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
{% endhighlight %}
The above configuration updates the package list, downloads, and installs available upgrades every day. The local download archive is cleaned every week.

	
You can read more about apt Periodic configuration options in the /etc/cron.daily/apt script header.

The results of unattended-upgrades will be logged to /var/log/unattended-upgrades.

Notifications
Configuring Unattended-Upgrade::Mail in /etc/apt/apt.conf.d/50unattended-upgrades will enable unattended-upgrades to email an administrator detailing any packages that need upgrading or have problems.

Another useful package is apticron. apticron will configure a cron job to email an administrator information about any packages on the system that have updates available, as well as a summary of changes in each package.

To install the apticron package, in a terminal enter:
{% highlight bash %}
sudo apt-get install apticron
{% endhighlight %}
Once the package is installed edit /etc/apticron/apticron.conf, to set the email address and other options:

  EMAIL="root@example.com"