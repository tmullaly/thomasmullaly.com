Someone is changing the display on our hp printer so I am using wireshark to track it down.

Here is the filter I am using:

  tcp.dstport == 9100 && tcp.flags.push == 1