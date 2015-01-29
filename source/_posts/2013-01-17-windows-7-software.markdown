This page is to document what software is needed to be installed on the Windows Desktops in the [[http://www.cs.umb.edu/sp/about/facilities/|Unix Lab]].  The table will document whether the software is to be included on the deployment image or to be deployed via GPO.  The table will also include what mechanism to update the software.  Certain software packages need to be updated more frequently due to security concerns.  If the solution for updating is Re-Imaging then automatic update checking should be turned off because the user won't be able to update anyway.

^ Software Name                ^ Vendor      ^ Deployment Method   ^ Update Method  ^
| Windows 7                       | Microsoft   | Image               | WSUS           |
| Office Professional Plus 2010   | Microsoft   | Image               | WSUS           |
| Office Project Professional 2010| Microsoft   | Image               | WSUS           |
| Adobe Flash Player 11           | Adobe       | Image               | WSUS           |
| Adobe Reader                    | Adobe       | Image               | ???            |
| CyberLink PowerDVD 9.5          | CyberLink   | Image               | Re-Image       |
| Google Chrome                   | Google      | ???                 | [[http://support.google.com/chromeos/a/bin/answer.py?hl=en&answer=188447|Chrome Browser FAQ for Admins]] |
| GSview 5.0                      | Ghostgum    | Image               | Re-Image       |
| Java SE Developer Kit 7         | Oracle      | ???                 | ???            |
| McAfee VirusScan Ent.           | McAfee      | Image               | McAfee Server  |
| Microsoft Visual Studio 2010    | Microsoft   | Image               | WSUS           |
| Mozilla Firefox                 | Mozilla     | Image               | Self-updates?  |
| NetBeans IDE 7.2.1              | NetBeans.org| Image               | Re-Image       |
| PuTTY                           | Simon Tatham| Image               | Re-Image       |
| Python 2.7.3 (64-bit)           | Python Soft | Image               | Re-Image       |
| QtSpim                          | Larus-Stone | Image               | Re-Image       |
| SSH Secure Shell                | F-Secure    | Image               | Re-Image       |
| VMware vSphere Client 5.0       | VMware Inc. | Image               | Re-Image       |
| VMware Workstation 8            | VMware Inc. | Image               | Re-Image       |
| WinZip 17.0                     | WinZip      | Image               | Re-Image       |
| Wireshark 1.8.3                 | Wireshark   | Image               | Re-Image       |