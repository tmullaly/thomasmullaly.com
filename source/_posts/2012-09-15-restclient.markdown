---
layout: post
title: "REST Client"
date: 2012-09-15
comments: true
categories: rest
---

Fire up firefox and download the RESTClient Add-on.

Window -> Add-ons Manager

Search -> RESTClient

{% highlight bash %}
Method: 
  POST

URL: 
  http://ec2-184-73-40-56.compute-1.amazonaws.com/sessions.json

Headers: 
  Content-Type: application/json

Body:
  {
    "user": 
    {
      "email": "your@emailaddress.com", "password": "Your_Password"
    }
  }
{% endhighlight %}