---
layout: post
title:  "Rails Developerment Environment Help"
date:   2013-04-04 09:27:50
categories: ruby
---
Apple's OS X Mountain Lion can be tricky to set up here's how we solved one problem
{% highlight bash %}
rvm remove 2.0.0 # get rid of unsuccessful installation
rvm get head --autolibs=3 # get the latest RVM and build required libs
rvm requirements # just in case, install all other required stuff
rvm install ruby-2.0.0
rvm --default use ruby-2.0.0
{% endhighlight %}