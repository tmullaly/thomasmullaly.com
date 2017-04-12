---
layout: post
title: "My IT Documentation Framework Template"
date: 2017-02-10 09:18:46 -0400
comments: true
categories: linux
---
I like using wiki's for documentation.  Here's my latest template headers, each server should have a page with this documented.

{% highlight bash %}
    1 Hardware
    2 Location
    3 Owner
    4 Stakeholders
    5 Purpose
    6 Software
        6.1 Operating System
        6.2 Services
    7 Support
    8 Configuration Management
    9 Monitoring
    10 Out of Band Management
    11 Security
        11.1 Firewall
        11.2 Patch Schedule
        11.3 Log Management
        11.4 Malware Defenses
        11.5 Incident Response Plan
        11.6 Penetration Tests
    12 Backups
        12.1 How and where is it backed up
        12.2 How to perform restore
    13 Business Continuity
{% endhighlight %}