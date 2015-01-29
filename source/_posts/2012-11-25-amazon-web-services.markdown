---
layout: post
title:  "Amazon Web Services"
date:   2012-11-25
categories: ["linux", "ec2"]
---
I got my fist EC2 instance last weekend.

http://ec2-184-73-40-56.compute-1.amazonaws.com is running
http://www.catornotcat.com Cat (or Not Cat)

##How to change the time zone on AWS EC2 Linux machine

Different servers available in the cloud will have different time zone. For running some crons in your time zone, this can be achieved by changing the time zone of the machine. This is something easy on the Linux box. You just need is to follow the following steps:

1) Remove the localtime
{% highlight bash %}
rm /etc/localtime
{% endhighlight %}
2) All the time zone are placed in the location /usr/share/zoneinfo/, now you need is to create a soft link of the appropriate time zone to /etc/localtime. For ex: if you want to change the timezone to be changed to the EST. You just have to execute the command given below:
{% highlight bash %}  
ln -s /usr/share/zoneinfo/EST /etc/localtime
{% endhighlight %}
Thats it. Now the time zone of the machine has been changed to EST.