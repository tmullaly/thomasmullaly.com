---
layout: post
title:  "Rails Production Server"
date:   2012-09-12
categories: rails
---

Load Ubuntu 12.04

Get updates.  Run 'sudo apt-get update' and 'sudo apt-get upgrade' and 'sudo apt-get dist-upgrade'

Reboot.

Install Apache2
{% highlight bash %}
sudo apt-get install apache2
{% endhighlight %}

Install curl
{% highlight bash %}
sudo apt-get install curl
{% endhighlight %}
  
Install rvm (Multi-User install)
{% highlight bash %}
curl -L https://get.rvm.io | sudo bash -s stable
{% endhighlight %}
  
Install Ruby Dependencies:
{% highlight bash %}
apt-get install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion
{% endhighlight %}
  
Install the latest version of ruby
{% highlight bash %}
rvm install ruby
{% endhighlight %}

Install the rails gems
{% highlight bash %}
gem install rails
{% endhighlight %}
  
Install The Ruby Racer
{% highlight bash %}
gem install therubyracer
{% endhighlight %}
  
Install nodejs
{% highlight bash %}
apt-get install nodejs
{% endhighlight %}
  
Get ready for passenger
{% highlight bash %}
apt-get install libcurl4-openssl-dev apache2-prefork-dev libapr1-dev libaprutil1-dev
gem install passenger
{% endhighlight %}

Install the passenger module
{% highlight bash %}
passenger-install-apache2-module
{% endhighlight %}
  
Edit your Apache configuration file, and add these lines:
{% highlight bash %}
LoadModule passenger_module /usr/local/rvm/gems/ruby-1.9.3-p194/gems/passenger-3.0.13/ext/apache2/mod_passenger.so
PassengerRoot /usr/local/rvm/gems/ruby-1.9.3-p194/gems/passenger-3.0.13
PassengerRuby /usr/local/rvm/wrappers/ruby-1.9.3-p194/ruby
{% endhighlight %}

Deploying a Ruby on Rails application: an example

Suppose you have a Rails application in /somewhere. Add a virtual host to your
Apache configuration file and set its DocumentRoot to /somewhere/public:
{% highlight bash %}
   <VirtualHost *:80>
      ServerName www.yourhost.com
      # !!! Be sure to point DocumentRoot to 'public'!
      DocumentRoot /somewhere/public    
      <Directory /somewhere/public>
         # This relaxes Apache security settings.
         AllowOverride all
         # MultiViews must be turned off.
         Options -MultiViews
      </Directory>
   </VirtualHost>
{% endhighlight %}

And that's it! You may also want to check the Users Guide for security and
optimization tips, troubleshooting and other useful information:

  /usr/local/rvm/gems/ruby-1.9.3-p194/gems/passenger-3.0.13/doc/Users guide Apache.html


Add deploy user and add rvm group
{% highlight bash %}
useradd deploy -d /home/deploy -m deploy
passwd deploy
usermod -a -G rvm deploy
{% endhighlight %}