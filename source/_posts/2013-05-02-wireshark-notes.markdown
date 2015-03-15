---
layout: post
title:  "Wireshark Notes"
date:   2013-05-02
categories: ["security","networking"]
---

Someone is changing the display on our hp printer so I am using wireshark to track it down.

Here is the filter I am using:
{% highlight bash %}
tcp.dstport == 9100 && tcp.flags.push == 1
{% endhighlight %}