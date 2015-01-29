---
layout: post
title:  "Disk Usage"
date:   2012-06-05
categories: linux
keywords: "Linux, Unix"
description: "How to check your disk usage in linux" 
---

###Account Quotas

Your user account has a home directory which includes such things as your mailbox and mail folders, application data, configuration files ("dotfiles"), browser cache, and public web space. The Mail quota is set to 2 MB soft, 5 MB hard.  Your home directory is not set programmatically, but the policy is 200 MB.

###Checking Disk Usage

You can check your own disk usage by executing this command in your home directory:
{% highlight bash %}
du -sh .
{% endhighlight %}
  
For a comprehensive audit of the directories in your home directory try this command:
{% highlight bash %}
du -sk ~/* ~/.??* | sort -n
{% endhighlight %}
  
Note: you should be logged into a linux workstation or server (i.e. linux1.cs.umb.edu), the older solaris operating system doesn't have the newer features in the du command.

You will start filling up your home directory if you start using the linux desktops in the cs unix lab.  Specifically, when you use a web browser on a linux workstation, the browser will begin caching in your home directory.  Chrome and Firefox use different directories for their caching and these directories will grow exponentially very quickly. 