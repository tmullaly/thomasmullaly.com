Group Policy provides an infrastructure for centralized configuration management of the operating system and applications that run on the operating system.  It is not enabled by default.

You can manage local and domain Group Policy by using domain-based versions of Windows Server 2008 R2. Although the Group Policy Management Console (GPMC) is distributed with Windows Server 2008 R2, you must install Group Policy Management as a feature through Server Manager.

You can also manage local and domain Group Policy by using Windows 7. For managing local Group Policy, the Group Policy Object Editor has been replaced by the Local Group Policy Editor. To manage domain Group Policy, you must first install the GPMC. The GPMC is included with RSAT, which is available for download:

[[http://go.microsoft.com/fwlink/?LinkId=130862|Windows Server 2008 R2 Remote Server Administration Tools for Windows 7]]

RSAT enables IT administrators to remotely manage roles and features in Windows Server 2008 R2 from a computer that is running Windows 7. RSAT includes support for the remote management of computers that are running either a Server Core installation or the full installation option of Windows Server 2008 R2. The functionality RSAT provides is similar to Windows Server 2003 Administration Tools Pack.

Installing RSAT does not automatically install the GPMC. To install the GPMC after you install RSAT, click Programs in Control Panel, click Turn Windows features on or off, expand Remote Server Administration Tools, expand Feature Administration Tools, and select the Feature Administration Tools and Group Policy Management Tools check boxes.



