---
layout: post
title: "Node.js and nginx on an Amazon Linux EC2 instance"
date: 2014-11-02 18:38:02 -0500
comments: true
categories: ['linux', 'nginx', 'node.js', 'aws', 'devops']
---
I'm looking to automate a node.js application on amazon web services.  I'm using the amazon linux ami, which is based off of centos 5.  Notice that amazon has switched from using para-virtualized (pv) guests to hardware virtualized machine guests (hvm).

Launch the instance and ssh in, and become root (sudo -i).
<!-- more -->
Now install nginx
{% highlight bash %}
[ec2-user@ip-172-30-0-164 ~]$ sudo !!
sudo yum install nginx
Loaded plugins: priorities, update-motd, upgrade-helper
amzn-main/latest                                                                     | 2.1 kB     00:00     
amzn-updates/latest                                                                  | 2.3 kB     00:00     
Resolving Dependencies
--> Running transaction check
---> Package nginx.x86_64 1:1.6.2-1.22.amzn1 will be installed
--> Processing Dependency: gd for package: 1:nginx-1.6.2-1.22.amzn1.x86_64
--> Processing Dependency: GeoIP for package: 1:nginx-1.6.2-1.22.amzn1.x86_64
--> Processing Dependency: libprofiler.so.0()(64bit) for package: 1:nginx-1.6.2-1.22.amzn1.x86_64
--> Processing Dependency: libGeoIP.so.1()(64bit) for package: 1:nginx-1.6.2-1.22.amzn1.x86_64
--> Processing Dependency: libgd.so.2()(64bit) for package: 1:nginx-1.6.2-1.22.amzn1.x86_64
--> Running transaction check
---> Package GeoIP.x86_64 0:1.4.8-1.5.amzn1 will be installed
---> Package gd.x86_64 0:2.0.35-11.10.amzn1 will be installed
--> Processing Dependency: libXpm.so.4()(64bit) for package: gd-2.0.35-11.10.amzn1.x86_64
---> Package gperftools-libs.x86_64 0:2.0-11.5.amzn1 will be installed
--> Processing Dependency: libunwind.so.8()(64bit) for package: gperftools-libs-2.0-11.5.amzn1.x86_64
--> Running transaction check
---> Package libXpm.x86_64 0:3.5.10-2.9.amzn1 will be installed
---> Package libunwind.x86_64 0:1.1-2.1.amzn1 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

============================================================================================================
 Package                    Arch              Version                         Repository               Size
============================================================================================================
Installing:
 nginx                      x86_64            1:1.6.2-1.22.amzn1              amzn-updates            523 k
Installing for dependencies:
 GeoIP                      x86_64            1.4.8-1.5.amzn1                 amzn-main               783 k
 gd                         x86_64            2.0.35-11.10.amzn1              amzn-main               155 k
 gperftools-libs            x86_64            2.0-11.5.amzn1                  amzn-main               570 k
 libXpm                     x86_64            3.5.10-2.9.amzn1                amzn-main                54 k
 libunwind                  x86_64            1.1-2.1.amzn1                   amzn-main                69 k

Transaction Summary
============================================================================================================
Install  1 Package (+5 Dependent packages)

Total download size: 2.1 M
Installed size: 4.8 M
Is this ok [y/d/N]: y
Downloading packages:
(1/6): GeoIP-1.4.8-1.5.amzn1.x86_64.rpm                                              | 783 kB     00:00     
(2/6): gd-2.0.35-11.10.amzn1.x86_64.rpm                                              | 155 kB     00:00     
(3/6): gperftools-libs-2.0-11.5.amzn1.x86_64.rpm                                     | 570 kB     00:00     
(4/6): libXpm-3.5.10-2.9.amzn1.x86_64.rpm                                            |  54 kB     00:00     
(5/6): libunwind-1.1-2.1.amzn1.x86_64.rpm                                            |  69 kB     00:00     
(6/6): nginx-1.6.2-1.22.amzn1.x86_64.rpm                                             | 523 kB     00:00     
------------------------------------------------------------------------------------------------------------
Total                                                                        15 MB/s | 2.1 MB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : libXpm-3.5.10-2.9.amzn1.x86_64                                                           1/6 
  Installing : gd-2.0.35-11.10.amzn1.x86_64                                                             2/6 
  Installing : libunwind-1.1-2.1.amzn1.x86_64                                                           3/6 
  Installing : gperftools-libs-2.0-11.5.amzn1.x86_64                                                    4/6 
  Installing : GeoIP-1.4.8-1.5.amzn1.x86_64                                                             5/6 
  Installing : 1:nginx-1.6.2-1.22.amzn1.x86_64                                                          6/6 
  Verifying  : gperftools-libs-2.0-11.5.amzn1.x86_64                                                    1/6 
  Verifying  : GeoIP-1.4.8-1.5.amzn1.x86_64                                                             2/6 
  Verifying  : 1:nginx-1.6.2-1.22.amzn1.x86_64                                                          3/6 
  Verifying  : gd-2.0.35-11.10.amzn1.x86_64                                                             4/6 
  Verifying  : libunwind-1.1-2.1.amzn1.x86_64                                                           5/6 
  Verifying  : libXpm-3.5.10-2.9.amzn1.x86_64                                                           6/6 

Installed:
  nginx.x86_64 1:1.6.2-1.22.amzn1                                                                           

Dependency Installed:
  GeoIP.x86_64 0:1.4.8-1.5.amzn1   gd.x86_64 0:2.0.35-11.10.amzn1   gperftools-libs.x86_64 0:2.0-11.5.amzn1
  libXpm.x86_64 0:3.5.10-2.9.amzn1 libunwind.x86_64 0:1.1-2.1.amzn1

Complete!
{% endhighlight %}
Make sure nginx starts up on a reboot:
{% highlight bash %}
sudo chkconfig --level 345 nginx on
{% endhighlight %}

Now Let's install node.js
{% highlight bash %}
[root@ip-172-30-0-164 ~]# curl -sL https://rpm.nodesource.com/setup | bash -

## Inspecting system...

+ rpm -q --whatprovides redhat-release
+ uname -m

## Confirming "el7-x86_64" is supported...

+ curl -sLf -o /dev/null 'https://rpm.nodesource.com/pub/el/7/x86_64/nodesource-release-el7-1.noarch.rpm'

## Downloading release setup RPM...

+ mktemp
+ curl -sL -o '/tmp/tmp.tGqnuw1nK9' 'https://rpm.nodesource.com/pub/el/7/x86_64/nodesource-release-el7-1.noarch.rpm'

## Installing release setup RPM...

+ rpm -i --nosignature --force '/tmp/tmp.tGqnuw1nK9'

## Cleaning up...

+ rm -f '/tmp/tmp.tGqnuw1nK9'

## Checking for existing installations...

+ rpm -qa 'node|npm' | grep -v nodesource

## Run `yum install -y nodejs` (as root) to install Node.js and npm.
## You may also need development tools to build native addons:
##   `yum install -y gcc-c++ make`

[root@ip-172-30-0-164 ~]# yum install -y nodejs
Loaded plugins: priorities, update-motd, upgrade-helper
nodesource/x86_64                                                                    | 2.4 kB     00:00     
nodesource/x86_64/primary_db                                                         | 7.9 kB     00:00     
Resolving Dependencies
--> Running transaction check
---> Package nodejs.x86_64 0:0.10.33-1nodesource.el7.centos will be installed
--> Finished Dependency Resolution

Dependencies Resolved

============================================================================================================
 Package           Arch              Version                                    Repository             Size
============================================================================================================
Installing:
 nodejs            x86_64            0.10.33-1nodesource.el7.centos             nodesource            4.5 M

Transaction Summary
============================================================================================================
Install  1 Package

Total download size: 4.5 M
Installed size: 15 M
Downloading packages:
warning: /var/cache/yum/x86_64/latest/nodesource/packages/nodejs-0.10.33-1nodesource.el7.centos.x86_64.rpm: Header V3 RSA/SHA1 Signature, key ID 34fa74dd: NOKEY
Public key for nodejs-0.10.33-1nodesource.el7.centos.x86_64.rpm is not installed
nodejs-0.10.33-1nodesource.el7.centos.x86_64.rpm                                     | 4.5 MB     00:00     
Retrieving key from file:///etc/pki/rpm-gpg/NODESOURCE-GPG-SIGNING-KEY-EL
Importing GPG key 0x34FA74DD:
 Userid     : "NodeSource <gpg-rpm@nodesource.com>"
 Fingerprint: 2e55 207a 95d9 944b 0cc9 3261 5ddb e8d4 34fa 74dd
 Package    : nodesource-release-el7-1.noarch (installed)
 From       : /etc/pki/rpm-gpg/NODESOURCE-GPG-SIGNING-KEY-EL
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
  Installing : nodejs-0.10.33-1nodesource.el7.centos.x86_64                                             1/1 
  Verifying  : nodejs-0.10.33-1nodesource.el7.centos.x86_64                                             1/1 

Installed:
  nodejs.x86_64 0:0.10.33-1nodesource.el7.centos                                                            

Complete!
{% endhighlight %}

Now install pm2 which is the daemon which runs the node server.

{% highlight bash %}
[root@ip-172-30-0-164 ~]# npm install pm2 -g --unsafe-perm
|
> pm2@0.11.1 preinstall /usr/lib/node_modules/pm2
> bash ./scripts/preinstall.sh

npm_config_argv={"remain":["pm2"],"cooked":["install","pm2","--global","--unsafe-perm"],"original":["install","pm2","-g","--unsafe-perm"]}
npm WARN optional dep failed, continuing fsevents@0.3.0
/usr/bin/pm2 -> /usr/lib/node_modules/pm2/bin/pm2

> pm2@0.11.1 postinstall /usr/lib/node_modules/pm2
> bash ./scripts/postinstall.sh

pm2@0.11.1 /usr/lib/node_modules/pm2
├── ikt@0.0.0
├── uid-number@0.0.5
├── json-stringify-safe@5.0.0
├── commander@2.3.0
├── eventemitter2@0.4.14
├── pm2-interface@2.0.4
├── colors@0.6.2
├── cron@1.0.4
├── cli-table@0.3.0
├── pidusage@0.1.0
├── async@0.9.0
├── axm@0.2.13
├── vizionar@0.1.3
├── chokidar@0.9.0 (recursive-readdir@0.0.2)
├── debug@2.0.0 (ms@0.6.2)
├── pm2-multimeter@0.1.2 (charm@0.1.2)
├── pm2-deploy@0.1.2 (tv4@1.0.18)
├── nssocket@0.5.1 (lazy@1.0.11)
├── chalk@0.5.1 (escape-string-regexp@1.0.2, ansi-styles@1.1.0, supports-color@0.2.0, strip-ansi@0.3.0, has-ansi@0.1.0)
├── punt@2.2.0 (amp-message@0.1.2)
├── pm2-axon-rpc@0.3.6 (commander@1.0.5)
├── pm2-axon@2.0.7 (amp-message@0.1.2, escape-regexp@0.0.1, configurable@0.0.1, amp@0.3.1)
├── pm2-rpc-fallback@3.0.9 (commander@1.0.5, axon@1.0.0)
├── coffee-script@1.8.0 (mkdirp@0.3.5)
├── pm2-logs@0.1.1 (chalk@0.4.0, pm2-interface@1.1.0, blessed@0.0.36)
└── moment@2.8.3
{% endhighlight %}

create a simple node.js server, create a file called server.js
{% highlight JavaScript %}
var http = require('http');

var server = http.createServer(function (request, response) {
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.end("Hello World\n");
});
server.listen(8080);
console.log("Server running at http://127.0.0.1:8080/");
{% endhighlight %}

You can try out the server by starting it like this:
{% highlight bash %}
[ec2-user@ip-172-30-0-164 ~]$ node server.js
Server running at http://127.0.0.1:8080/
{% endhighlight %}

Or you can daemonize it with pm2, like this:
{% highlight bash %}
[ec2-user@ip-172-30-0-164 ~]$ pm2 start server.js 
Initializing PM2 configuration (/home/ec2-user/.pm2)
Starting PM2 daemon...
[PM2] Process server.js launched
┌──────────┬────┬──────┬──────┬────────┬───────────┬────────┬────────────┬─────────────┐
│ App name │ id │ mode │ PID  │ status │ restarted │ uptime │     memory │    watching │
├──────────┼────┼──────┼──────┼────────┼───────────┼────────┼────────────┼─────────────┤
│ server   │ 0  │ fork │ 1257 │ online │         0 │ 0s     │ 9.922 MB   │ unactivated │
└──────────┴────┴──────┴──────┴────────┴───────────┴────────┴────────────┴─────────────┘
 Use `pm2 desc[ribe] <id>` to get more details
{% endhighlight %}

Now let's add the proxy info to nginx, edit /etc/nginx/nginx.conf
{% highlight bash %}
server {
        listen       80;
        #server_name  localhost;
        server_name  your-ip-address;
        root         /usr/share/nginx/html;

        #charset koi8-r;

        #access_log  /var/log/nginx/host.access.log  main;

        location / {
		proxy_pass http://localhost:8080;
		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection 'upgrade';
		proxy_cache_bypass $http_upgrade;
        }
{% endhighlight %}

restart nginx

{% highlight bash %}
sudo service nginx restart
{% endhighlight %}

hit the ip address with a browser and you should see "hello world"

next up is an ansible playbook to automate this process
