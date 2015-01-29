I cloned a new virtual Ubuntu 12.04 server and added isc's dhcp server to emulate the cs department's network.

  apt-get install dhcpd-server
  
This installs isc's dhcpd version 4 I think.

Edit /etc/dhcp/dhcpd.conf
<code>
default-lease-time 600;
max-lease-time 7200;
authoritative;
subnet 10.10.10.0 netmask 255.255.255.0 {
  range 10.10.10.100 10.10.10.200;
  option routers 10.10.10.1;
  option subnet-mask 255.255.255.0;
  option broadcast-address 10.10.10.255;
  option domain-name-servers 10.10.10.10, 10.10.10.11;
  option domain-name "tom.umb.edu";
  option domain-search "tom.umb.edu";
  
  # dynamic DNS updates
  ddns-updates on;
  ddns-domainname "tom.umb.edu";
  ddns-rev-domainname "10.10.10.in-addr.arpa";

  next-server 10.10.10.20;
  filename "boot\\x86\\wdsnbp.com"; 
}
</code>

Start the server:
  service isc-dhcp-server start