Deploying a Linux desktop will take a few steps.
1. WDS - pxelinux
2. debian preseed
3. puppet

pxelinux.cfg/default

<code>
LABEL ubuntu-804 MENU LABEL ^1. Ubuntu 12.04 Standard Desktop kernel ubuntu-8.04-installer/i386/linux append vga=normal initrd=ubuntu-8.04-installer/i386/initrd.gz debian-installer/framebuffer=true console-setup/ask_detect=false console-setup/layoutcode=gb locale=en_GB netcfg/wireless_wep= netcfg/choose_interface=eth0 netcfg/get_hostname= url=http://<your-webserver>/preseed/install.preseed netcfg/dhcp_timeout=60 splash --
</code>

The append line is quite lengthy and contains automations for things that can't be preseeded with the file from the webserver, because at that stage the network is not yet initialised. The install.preseed file is just a modified file from the example preseeding file, which can be found in the installation-guide-i686 debian package.

However we don't only want to install a standard desktop, we also want to make some automated customisations to it right after the installation. For this (and general configuration management) we use Puppet, which is packaged for Ubuntu. To get that activated, we append this line in the preseeding file:

<code>
d-i pkgsel/include string puppet 
</code>

