---
layout: post
title:  "How to Create an Active Directory Server"
date:   2012-10-18
categories: ["windows", "virtualization"]
---
##Clone a new server
{{ http://www.brosix.com/wp-content/uploads/Active-Directory-Logo-300x134.png?200&recache}}
Create a clone from the Windows 2008 R2 Gold Master

##Rename the Server
Call it AD
Shutdown the server

##Change the Network
While powered off, change the network settings

Change the Network to be **Custom (VMnet7)**

Power on the VM
##Change the Network Configuration
Open up the Network and Sharing Center

Click on Local Area Network

Click on Properties

Choose IPv4 and Click Properties

Make the network config as follows:
  IP address: 10.10.10.10
  Subnet mask: 255.255.255.0
  Default gateway: 10.10.10.1
  DNS: 127.0.0.1

Click on OK

##Install the Active Directory Role
Open the Server Manager

Click on Roles

Click on Add Roles

Next

Select Active Directory Domain Services

Reboot

Open Server Manager and Run the Active Directory Services Installation Wizard (dcpromo.exe)


Create a domain in a new forest

FQDN: tom.umb.edu

Functional Level: Windows Server 2008 R2

Make sure DNS sever is checked

Yes you want to continue

Reboot
