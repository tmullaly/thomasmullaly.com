---
layout: post
title: "How to Convert a Man Page to Text"
date: 2013-01-06
comments: true
categories: linux
---
At the command line, run:
{% highlight bash %}
$ man MANPAGE | col -b > filename.txt
{% endhighlight %}

Here's what it means:
{% highlight bash %}
man = display the man pages for a stated command

| = pipe the output of the man pages somewhere else

col -b = format the output

> = send the output to a file

filename.txt = name of the file you wish to create and populate with output
{% endhighlight %}