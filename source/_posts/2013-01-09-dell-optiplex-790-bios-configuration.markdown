====== Dell OptiPlex 790 BIOS Settings ======
  * Hit F12 to enter Boot Options

  * Choose BIOS Setup from OTHER OPTIONS

  * System Configuration -> Integrated NIC -> Enabled w/PXE

  * General -> Boot Sequence -> 
    * Only check: **Onboard NIC** and **P0: WDC WD5000AAKX-753CA1**
    * Make the **OnBoard NIC** the first option in the list

  * <del>USB Configuration -> Uncheck Enable Boot Support</del>

  * Power Management -> Wake on LAN -> LAN only