---
layout: post
title:  "Apache Security and Hardening"
date:   2012-06-04
categories: ["security", "apache"]
keywords: "Security, Linux, Apache"
description: "How to security harden the apache web server" 
---
##Disable Apache Signature and/or Apache Banner
Apache Signature or Apache Banner is basically the same thing. It is an application name together with version name that is printed when performing a web request. Nobody actually needs this information at all, but it is enabled by default. You need to alter the Apache configuration file to disable it.
{% highlight bash %}
ServerSignature Off
ServerTokens Prod
{% endhighlight %}
In Ubuntu, you need to change the following file: /etc/apache2/conf.d/security

Double check that ServerSignature and ServerTokens configuration settings are not enabled in some other parts of the configuration file.

To test:
{% highlight bash %}
telnet remote_server.com 80
Trying remote_server.com...
Connected to remote_server.com.
Escape character is '^]'.
HEAD / HTTP/1.0            <- after this press 2 times ENTER
{% endhighlight %}

##The Trace HTTP Request
HTTP TRACE request is used to echo back all received information. It can be tricked to print HTTP cookies and as a result steal HTTP session. Basically this request can be used as part of the Cross Site Scripting attack, or XSS. It is recommended to disable it as a security precaution.

Add the following to the web-server's configuration file. For example alter the following file in Ubuntu: /etc/apache2/conf.d/security
{% highlight bash %}
TraceEnable off
{% endhighlight %}

##Disable directory indexing
Directory indexing is a features found in every web-server by default. When directory indexing is enabled, the web-site prints a list of files found in the website directories when the default page does not exists (for example index.php). Directories reported can be viewed by any visitor. It is vulnerable in the sense that these directories can contain configuration, private and backup files which can be used by the attackers to take your server under control.

You can fix this problem by disabling the Apache autoindex module. In some Apache installations it is called mod_autoindex.so. In Ubuntu, you just need to remove the following files:
{% highlight bash %}
/etc/apache2/mods-enabled/autoindex.load
/etc/apache2/mods-enabled/autoindex.conf
{% endhighlight %}
So you can do it running the following commands:
{% highlight bash %}
rm -f /etc/apache2/mods-enabled/autoindex.load
rm -f /etc/apache2/mods-enabled/autoindex.conf
{% endhighlight %}

##Disable WebDAV
WebDAV is a file access protocol created over HTTP protocol. It allows you to upload and download files, and change file contents from the website. This service is required only in very rare cases. From our experience, this feature was only required to run SVN server (link). Make sure that WebDAV is disabled in production websites. When WebDAV is enabled, the following commands are supported by Apache: OPTIONS, PROPFIND, etc. These commands are sensitive from computer security point of view.

You can fix this problem by disabling Apache dav, dav_fs and dav_lock modules. In Ubuntu you just need to remove the following files:
{% highlight bash %}
/etc/apache2/mods-enabled/dav.load
/etc/apache2/mods-enabled/dav_fs.conf
/etc/apache2/mods-enabled/dav_fs.load
/etc/apache2/mods-enabled/dav_lock.load
{% endhighlight %}
So you can do it running the following commands:
{% highlight bash %}
rm -f /etc/apache2/mods-enabled/dav.load
rm -f /etc/apache2/mods-enabled/dav_fs.conf
rm -f /etc/apache2/mods-enabled/dav_fs.load
rm -f /etc/apache2/mods-enabled/dav_lock.load
{% endhighlight %}