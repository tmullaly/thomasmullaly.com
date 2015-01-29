Creating Computer Account Objects in AD DS
====== Pre Stage the client computer account ======
You can use Windows Deployment Services to link physical computers to computer account objects in Active Directory Domain Servers (AD DS). This is called prestaging the client. Prestaged clients are also called known computers. This allows you to then configure properties on the computer account to control the installation for the client. For example, you can configure the network boot program and the unattend file that the client should receive, as well as the server from which the client should download the network boot program. You can create a computer account object and associate it with a physical computer using the following methods:

    * Using WDSUTIL. You can prestage client computers before they have attempted a network boot, by running WDSUTIL /Add-Device /Device:<name> /ID:<ID>. You cannot prestage computers by using the Windows Deployment Services MMC snap-in, but you can set the Auto-Add policy and approve or reject pending computers.
    * Using the Active Directory Users and Computers snap-in. You can prestage client computers before they have attempted a network boot using AD DS. For instructions, see the section "To prestage a client computer" in How to Manage Client Computers.
    * Enabling the Auto-Add policy. If you enable this policy, when you approve the installation for an unknown client, the installation will proceed and a computer account will be created in AD DS for the client. For more information, see Enabling the Auto-Add Policy
    * Using Windows Deployment Services as part of the image installation. By default, all operating system installations using Windows Deployment Services result in a client computer that is joined to a domain. You can disable this functionality using the Client tab of the server’s properties

http://technet.microsoft.com/en-us/library/cc771206(v=ws.10).aspx


WDSUTIL /Add-Device /Device:W7PreStage /ID:000c29d2cc4f /JoinDomain:Yes /OU:OU=Workstations /Domain:OU=Workstations,DC=tom,DC=umb,DC=edu
