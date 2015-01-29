---
layout: post
title:  "Ruby on Rails at UMB"
date:   2013-04-04
categories: rails
---
[Ruby on Rails Links for CS410]({% post_url 2013-06-12-ruby-on-rails-links-for-cs410 %})

[Production Rails Server for CS410]({% post_url 2013-04-06-production-rails-server-for-cs410 %})

[Development Environment Help]({% post_url 2013-04-04-development-environment-help %})

I've installed rvm globally on each linux workstation.

<!-- more -->

First, make sure you speak with me first, I need to add you into the proper unix group.

1. Check your group to make sure you are in the rvm group
{% highlight bash %}
tmullaly@yel:~$ id
uid=7265(tmullaly) gid=10241(ugrad) groups=10241(ugrad),1080(cs108-1),20106(rvm)
{% endhighlight %}

2. Add this rvm directive to your .bashrc
{% highlight bash %}
[[ -s "/etc/profile.d/rvm.sh" ]] && . "/etc/profile.d/rvm.sh" # Load RVM function
{% endhighlight %}
  
3. Logout and log back in to make sure your .bashrc has been sourced

4. Check to see if ruby and gem is now in your path
{% highlight bash %}
tmullaly@yel:~$ which ruby
/usr/local/rvm/rubies/ruby-2.0.0-p0/bin/ruby
tmullaly@yel:~$ which gem
/usr/local/rvm/rubies/ruby-2.0.0-p0/bin/gem
{% endhighlight %}

5. Check what gems are installed
{% highlight bash %}
tmullaly@yel:~$ gem list

*** LOCAL GEMS ***

bundler (1.3.0)
rake (10.0.3)
rubygems-bundler (1.1.1)
rvm (1.11.3.6)
{% endhighlight %}

6. install rails, or whatever gem you need
{% highlight bash %}
gem install rails
{% endhighlight %}
Gems get install globally per machine, if you install a gem on red, I can use it.  If you install a gem on blue and then you login to cyan, you need to install it again.
  
  
Here are some more notes to update to a newer version of rvm and/or ruby
{% highlight bash %}
rvm get stable
rvm install ruby
rvm --default use 2.0.0
{% endhighlight %}