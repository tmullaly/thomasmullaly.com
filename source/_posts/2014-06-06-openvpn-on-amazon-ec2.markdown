---
layout: post
title:  "OpenVPN on Amazon ec2"
date:   2014-06-06
categories: security
---
Create a new EC2 instance, I use the offical Amazon AMI images becasue it's the least expensive option.

Login to the new instance using the pem and run the updates.
{% highlight bash %}
ssh -i my.pem ec2-user@ec2-xxx-xxx-xxx-xxx.compute-1.amazonaws.com
yum update
reboot
{% endhighlight %}

Let's install openvpn
<!-- more -->
{% highlight bash %}
yum install openvpn easy-rsa -y --enablerepo=epel
{% endhighlight %}
Let's copy the sample server configuration file into place, the location of the file might be different for you:
{% highlight bash %}
cp /usr/share/doc/openvpn-*/sample/sample-config-files/server.conf /etc/openvpn
{% endhighlight %}
Edit the server.conf file
{% highlight bash %}
vi /etc/openvpn/server.conf
{% endhighlight %}
Uncomment and modify these lines:
{% highlight bash %}
push "redirect-gateway def1 bypass-dhcp"
push "dhcp-option DNS 8.8.8.8"
push "dhcp-option DNS 8.8.4.4"
user nobody
group nobody
{% endhighlight %}
Now on to easy rsa
{% highlight bash %}
cp -rf /usr/share/easy-rsa/2.0/* /etc/openvpn/easy-rsa
vi /etc/openvpn/easy-rsa/vars
{% endhighlight %}
Make these changes to vars:
{% highlight bash %}
export KEY_COUNTRY="US"
export KEY_PROVINCE="MA"
export KEY_CITY="Boston"
export KEY_ORG="Thunderhouse"
export KEY_EMAIL="tom@thunderhouse.com"
export KEY_CN=vpn.thunderhouse.com
export KEY_NAME=server
export KEY_OU=server
{% endhighlight %}
Copy the OpenSSL configuration into place:
{% highlight bash %}
cp /etc/openvpn/easy-rsa/openssl-1.0.0.cnf /etc/openvpn/easy-rsa/openssl.cnf
{% endhighlight %}
Generate the server crypto stuff:
{% highlight bash %}
cd /etc/openvpn/easy-rsa
source ./vars
./clean-all
./build-ca
./build-key-server server
./build-dh
cd /etc/openvpn/easy-rsa/keys
cp dh2048.pem ca.crt server.crt server.key /etc/openvpn
{% endhighlight %}
Generate the client certs:
{% highlight bash %}
cd /etc/openvpn/easy-rsa
./build-key client
{% endhighlight %}
{% highlight bash %}
   27  mkdir -p /etc/openvpn/easy-rsa/keys
   35  yum install lzo
   36  yum-config-manager --enable epel
   37  yum install easy-rsa
   42  cp -rf /usr/share/easy-rsa/2.0/* /etc/openvpn/easy-rsa
   56  vi vars 
   57  source ./vars
   58  ./clean-all
   59  ./build-ca
   65  ./build-key-server server
   66  ./build-dh 
   67  cd keys/
   69  cp dh2048.pem ca.crt server.crt server.key /etc/openvpn
   70  cd ..
   72  ./build-key macbookair
{% endhighlight %}
Create your iptables rule to allow NATing (routing) from the vpn subnet and save those tables to disk.
{% highlight bash %}
iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE
service iptables save
{% endhighlight %}
Now, add IP forwarding in sysctl
{% highlight bash %}
vi /etc/sysctl.conf
net.ipv4.ip_forward = 1
{% endhighlight %}
{% highlight bash %}
client
dev tun
proto udp
remote 52.201.15.218 1194
resolv-retry infinite
nobind
persist-key
persist-tun
comp-lzo
verb 4
ca ca.crt
cert client.crt
key client.key
ns-cert-type server
{% endhighlight %}
Let's install netstat-nat to help trouble-shoot networking:
{% highlight bash %}
yum install netstat-nat
yum install iptstate
{% endhighlight %}
{% highlight bash %}
  154  exit
  155  tail -f /var/log/messages 
  156  history
{% endhighlight %}
