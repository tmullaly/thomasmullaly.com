---
layout: post
title:  "Ubuntu Linux 12.04 LTS"
date:   2013-03-29
categories: linux
---
##NetworkManager
NetworkManager sucks, at least in our lab environment.  I don't want users messing around with any network settings, creating vpns or managing a wireless card.  Our desktops and servers don't move around so the advantages of using it do not exist.

Remove it!!!
{% highlight bash %}
apt-get purge network-manager
{% endhighlight %}  
Now edit: /etc/network/interfaces
{% highlight bash %}
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
{% endhighlight %}

##Avahi-daemon
Remove it!!!
{% highlight bash %}
apt-get remove avahi-daemon
{% endhighlight %}

##Change the Unity login screen
{% highlight bash %}
vi /etc/lightdm/lightdm.conf

[SeatDefaults]
user-session=ubuntu
greeter-session=unity-greeter
greeter-show-manual-login=true
allow-guest=false
greeter-hide-users=true
{% endhighlight %}

##Mail
First get autofs in place
{% highlight bash %}
apt-get install mailutils postfix
{% endhighlight %}  
look at the following files and configure
  /etc/mailname
  /etc/postfix/main.cf
  
setup sym link to enable automounting
  rm -rf /var/mail
  ln -s /spool/mail /var/mail
  
##Install a printer
If you have a cups server, all you need is the cups client.
see here: http://www.debianadmin.com/setup-cups-common-unix-printing-system-server-and-client-in-debian.html

  apt-get install cups-client
  apt-get install a2ps
<del>apt-get install cups-bsd</del>


{% highlight bash %}
ServerName sf01.cs.umb.edu
#ServerName ip-address[:port]

#Encryption Always
#Encryption Never
#Encryption Required
Encryption IfRequested
{% endhighlight %}

{% highlight bash %}
  service cups restart
{% endhighlight %}
##Install ant
{% highlight bash %}
sudo apt-get install ant ant-optional
{% endhighlight %}

##Install Google Chrome on Ubuntu 12.04
Install chrome from google official repository.

First we need to add the repository source to the update manager.

Step 1 » Setup key:
{% highlight bash %}
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
{% endhighlight %}

Step 2 » Type this command exactly to add chrome repositories:
{% highlight bash %}
sudo sh -c 'echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
{% endhighlight %}

Step 3 » Now Update the package list
{% highlight bash %}
sudo apt-get update
{% endhighlight %}

Step 4 » Finally install google chrome
{% highlight bash %}
sudo apt-get install google-chrome-stable
{% endhighlight %}

This should auto update chrome whenever the system does an upgrade.

###List packages installed
  apt-cache pkgnames > pkgnames
###Install from list of packages
  cat pkgnames | xargs apt-get install
###Automatic Updating
  apt-get install unattended-upgrades

  /etc/apt/apt.conf.d/10periodic 
{% highlight bash %}
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
{% endhighlight %}


  /etc/apt/apt.conf.d/50unattended-upgrades
{% highlight bash %}
// Automatically upgrade packages from these (origin:archive) pairs
Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}-security";
        "${distro_id}:${distro_codename}-updates";
//      "${distro_id}:${distro_codename}-proposed";
//      "${distro_id}:${distro_codename}-backports";
	"Google\, Inc.:stable";
};

// List of packages to not update
Unattended-Upgrade::Package-Blacklist {
//      "vim";
//      "libc6";
//      "libc6-dev";
//      "libc6-i686";
};

// This option allows you to control if on a unclean dpkg exit
// unattended-upgrades will automatically run 
//   dpkg --force-confold --configure -a
// The default is true, to ensure updates keep getting installed
//Unattended-Upgrade::AutoFixInterruptedDpkg "false";

// Split the upgrade into the smallest possible chunks so that
// they can be interrupted with SIGUSR1. This makes the upgrade
// a bit slower but it has the benefit that shutdown while a upgrade
// is running is possible (with a small delay)
//Unattended-Upgrade::MinimalSteps "true";

// Install all unattended-upgrades when the machine is shuting down
// instead of doing it in the background while the machine is running
// This will (obviously) make shutdown slower
//Unattended-Upgrade::InstallOnShutdown "true";

// Send email to this address for problems or packages upgrades
// If empty or unset then no email is sent, make sure that you
// have a working mail setup on your system. A package that provides
// 'mailx' must be installed.
Unattended-Upgrade::Mail "root@localhost";

// Set this value to "true" to get emails only on errors. Default
// is to always send a mail if Unattended-Upgrade::Mail is set
//Unattended-Upgrade::MailOnlyOnError "true";

// Do automatic removal of new unused dependencies after the upgrade
// (equivalent to apt-get autoremove)
//Unattended-Upgrade::Remove-Unused-Dependencies "false";

// Automatically reboot *WITHOUT CONFIRMATION* if a 
// the file /var/run/reboot-required is found after the upgrade 
Unattended-Upgrade::Automatic-Reboot "true";


// Use apt bandwidth limit feature, this example limits the download
// speed to 70kb/sec
//Acquire::http::Dl-Limit "70";
{% endhighlight %}


  /etc/apt/apt.conf.d/20auto-upgrades
{% highlight bash %}
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
{% endhighlight %}
##Turn off and disable Whoopsie
New in Ubuntu 12.04 is whoopsie – which sends a crash log to ubuntu. Nice feature? Well no as they don’t tell you about it nor to they ask you, they just do it.

Nice privacy issue guys.

To tell if you have it check the file /etc/default/whoopsie

  [General]
  report_crashes=true

If you see that then it’s enabled. You’ll also see a process running called whoopsie as well.

To turn it off just change it from true to false & kill the whoopsie process (or reboot).

###last: /var/log/wtmp: No such file or directory
as a work around you can run:
  touch /var/log/{btmp,lastlog,wtmp}
  
###deja-dup-monitor
For deja-dup I did
{% highlight bash %}
#X-GNOME-Autostart-Delay=120
X-GNOME-Autostart-enabled=false
{% endhighlight %}
in /etc/xdg/autostart/deja-dup-monitor.desktop ...

You do not have to reboot, simple restart of X is sufficient...
  
##Ubuntu VM stall problem
Fixed!!!

The core switch was the problem, bad memory module.

Now java is killing the VM's

Re-Nice the processes:
{% highlight bash %}
renice +5 `pgrep java`
{% endhighlight %}