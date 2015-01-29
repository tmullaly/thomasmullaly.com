---
layout: post
title:  "How to zero out free disk space in Windows"
date:   2014-06-02
categories: security
---
To overwrite deleted data on a volume by using Cipher.exe, use the /w switch with the cipher command. Use the following steps:
<!-- more -->
1.  Quit all programs.

2.  Click Start, click Run, type cmd, and then press ENTER.

3.  Type cipher /w:driveletter:\foldername, and then press ENTER. Specify the drive and the folder that identifies the volume that contains the deleted data that you want to overwrite. Data that is not allocated to files or folders will be overwritten. This permanently removes the data. This can take a long time if you are overwriting a large space. 

Note With mount points in Windows 2000, you can mount a volume on any empty folder on an NTFS volume. When you do this, the mounted volume does not have a drive letter of its own. The only way to address that volume is by using the path where you created the mount point. Therefore, the /w switch requests a path of a folder, and from that, it determines the associated volume to wipe. Because of the way the file system works, the whole volume must be wiped. A file can be written anywhere on the volume at any time. A folder does not address a specific physical location on disk but is a logical container for file entries in the volume's table of contents (MFT or FAT). To make sure that there is no leftover data in unallocated space, all unallocated space on the volume must be wiped.
