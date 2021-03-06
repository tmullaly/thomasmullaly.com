======Synchronize time with external NTP server on Windows Server 2008======

Time synchronization is an important aspect for all computers on the network. By default, the clients computers get their time from a Domain Controller and the Domain Controller gets his time from the domain’s PDC Operation Master. Therefore the PDC must synchronize his time from an external source. I usually use the servers listed at the NTP Pool Project website. Before you begin, don’t forget to open the default UDP 123 port (in- and outbound) on your (corporate) firewall.

First, locate your PDC Server. Open the command prompt and type: 
  C:\>netdom /query fsmo
Log in to your PDC Server and open the command prompt.
Stop the W32Time service: 
  C:\>net stop w32time
Configure the external time sources, type: 
  C:\> w32tm /config /syncfromflags:manual /manualpeerlist:”0.pool.ntp.org, 1.pool.ntp.org, 2.pool.ntp.org”
Make your PDC a reliable time source for the clients. Type: 
  C:\>w32tm /config /reliable:yes
Start the w32time service: 
  C:\>net start w32time
The windows time service should begin synchronizing the time. You can check the external NTP servers in the time configuration by typing: 
  C:\>w32tm /query /configuration
You can also check the status:
  C:\Users\tmullaly>w32tm /query /status
  Leap Indicator: 0(no warning)
  Stratum: 3 (secondary reference - syncd by (S)NTP)
  Precision: -6 (15.625ms per tick)
  Root Delay: 0.0978394s
  Root Dispersion: 7.8137172s
  ReferenceId: 0xCE392C11 (source IP:  206.57.44.17)
  Last Successful Sync Time: 12/18/2012 7:15:14 PM
  Source: 1.pool.ntp.org
  Poll Interval: 6 (64s)

Check the Event Viewer for any errors.
Tested on Windows Server 2008 Standard

