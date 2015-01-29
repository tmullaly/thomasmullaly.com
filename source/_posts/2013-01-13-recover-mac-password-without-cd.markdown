---
layout: post
title:  "How to reset Mac Password without a CD"
date:   2013-01-13
categories: apple
---
Using a pretty nifty trick you can reset a forgotten Mac password without a Mac OS X installer CD/DVD. The steps may seem a little intimidating at first but I assure you it’s easy if you follow them exactly, here is exactly how to do this in three stages:

###Stage 1) Boot into Single User Mode and remove a setup file

Restart the Mac holding down the Command+S keys, this will take you into Single User Mode and it’s Terminal interface
You’ll need to check the filesystem first:
{% highlight bash %}
fsck -fy
{% endhighlight %}

Next, you must mount the root drive as writeable so that changes will save:
{% highlight bash %}
mount -uw /
{% endhighlight %}

Now, type the following command exactly, followed by the enter key:
{% highlight bash %}
rm /var/db/.applesetupdone
{% endhighlight %}

After removing the applesetupdone file, you need to reboot, type ‘reboot’ and hit enter

###Stage 2) Create a New User Account upon System Boot
You aren’t finished, but the hard part is now over – no more command lines, you’ll now be in the familiar Mac OS X GUI to finish the password reset process. In this step we just create a new user account as if you just got a new Mac:

Upon reboot, you will be presented with the traditional “Welcome Wizard” startup screen just like when you first get a Mac
Follow the welcome wizard and create a new user account – making the account name different from the account whose password you want to recover
Continue on and boot into Mac OS X with this newly created user account, this new user account is an Administrator and has administrative access

###Stage 3) Reset the Forgot Password via System Preferences
You are almost done, now you just need to reset the forgotten user account password using the Accounts control panel:

Once you are booted into Mac OS X, click on the Apple logo and then navigate down to “System Preferences”
Click on the “Accounts” icon in System Preferences
Click on the Lock icon in the lower left corner of the “Accounts” preference window and enter the newly created user credentials, this enables you to change other user accounts and reset other users passwords
On the left side user panel, select the user account containing the forgotten password
With the user of the forgotten password account selected, click on the “Reset Password” button
Enter a new password for that user, be sure to include a meaningful hint so you don’t forget it again!
Close System Preferences and reboot the Mac
You can now login to the previously inaccessible user account using the newly reset password! All user files and settings are maintained as before the password was forgotten
Optional: If you’d like, you can delete the temporary account you created to reset the users password. This is wise for security purposes.

Here’s how this works: by deleting the .applesetupdone file, you are telling Mac OS X to re-run the setup wizard, which by default creates a new user account with Administrative abilities, which can then reset the forgotten password of any other user on the Mac. This is a great trick and excellent troubleshooting technique if you don’t have a Mac OS X installer CD/DVD laying around, which is pretty much the norm as many people tend to lose or misplace the installer disks that come with their computers. I have used this exact method multiple times to restore various Macs with forgotten/lost passwords.