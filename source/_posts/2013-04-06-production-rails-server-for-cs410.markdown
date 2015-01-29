---
layout: post
title:  "Production Rails Server for CS410"
date:   2013-04-06
categories: rails
---

##Stop Whoopsie
Edit /etc/default/whoopsie and change report_crashes to false
{% highlight bash %}
service whoopsie stop
{% endhighlight %}
##System ruby, gems and rails
We'll leave the system ruby in place but purge rails and gems:
{% highlight bash %}
apt-get purge rails rubygems
apt-get autoremove
{% endhighlight %}
<!-- more --> 

##Add a local user and give privileges
I created a local user because my production boxes are appliances and only has what needs to be installed on them.  This environment is different and I am adjusting my install to compensate.
##Install some basic packages
{% highlight bash %}
apt-get install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev  libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison pkg-config
{% endhighlight %}
##Install RVM in multi-user mode
We'll be installing ruby with rvm for each application
##Rails
make sure you're using the proper ruby and gem version
{% highlight bash %}
tom@nginx:~$ which gem ruby
/usr/local/rvm/rubies/ruby-2.0.0-p0/bin/gem
/usr/local/rvm/rubies/ruby-2.0.0-p0/bin/ruby
{% endhighlight %}
Install Rails
{% highlight bash %}
gem install rails --no-ri --no-rdoc
{% endhighlight %}
We don't need the Rails documentation or interactive on the server, infact you should add this to your ~/.gemrc
/home/{user}/.gemrc
{% highlight bash %}
# http://docs.rubygems.org/read/chapter/11
--- 
gem: --no-ri --no-rdoc
benchmark: false
verbose: true
update_sources: true
sources: 
- http://gems.rubyforge.org/
- http://rubygems.org/
backtrace: true
bulk_threshold: 1000
 
# HTTP Proxy options
# http-proxy: http://example.com:80
{% endhighlight %}

##Install Nginx
{% highlight bash %}
apt-get install python-software-properties
add-apt-repository ppa:nginx/stable
sudo apt-get update
sudo apt-get install nginx
{% endhighlight %}

##Install Unicorn
{% highlight bash %}
gem install unicorn
{% endhighlight %}
  
##Install NodeJS
{% highlight bash %}
apt-get install nodejs
{% endhighlight %}

##Test Unicorn
  - create a test app
  - cd to test_app
  - run:
{% highlight bash %}
unicorn_rails -p 3000
{% endhighlight %}

##Install rvm wrapper
This is for the unicorn init script.
{% highlight bash %}
rvm wrapper ruby-2.0.0-p0@global bootup unicorn
{% endhighlight %}
##Add unicron init script
Becasue I 'm always rebooting the server, unicorn needs an init script:

[Unicorn Init Script]({% post_url 2013-04-06-unicorn-init-script %})

##Install Mysql
{% highlight bash %}
apt-get install mysql-server mysql-client mysql-common libmysql-ruby libmysqlclient-dev
{% endhighlight %}