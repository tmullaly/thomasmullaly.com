Install [[Oracle Solaris 11]]

Install OpenCSW
  pkgadd -d http://get.opencsw.org/now
  
Add /opt/csw/bin to path
  export PATH=$PATH:/opt/csw/bin
  
Install the puppet agent
  pkgutil --install puppet
  
The puppet agent will auto start, if you need to add the puppet master to /etc/hosts and restart puppet:

  svcs
  
this prints out the services running

  svcadm restart cswpuppetd