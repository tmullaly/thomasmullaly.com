I added another virtual drive to store the images on and to capture to.

It's mounted on E:

To move the RemoteInstall Directory:

1. First you have to uninitialize
  C:/> WDSUTIL /uninitialize-server
  
2. Copy the RemoteInstall Directory to the new location

3. Now reinitialize
  c:/> WDSUTIL /Initialize-server /RemInst:E:\RemoteInstall