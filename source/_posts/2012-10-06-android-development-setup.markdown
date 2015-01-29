{{ http://developer.android.com/images/brand/Android_Robot_100.png?recache}}
All development has to be done locally for now.
I created a /android directory on each linux machine and put eclipse and the Android sdk in there.  I might find a place on the network, but the linux desktops are not mounting /tools (yet).

To use it, start eclipse using the absolute path, if you don't use the absolute path, you'll be using the system eclipse which is older and has no android sdk.
  /android/eclipse/eclipse
  
The Android virtual devices HAVE TO BE ON A LOCAL DRIVE!!!
We're using /tmp to keep the AVD's in, so you'll probably have to recreate the AVD's each time you login.  It's the best I can come up with in this environment.
Create a sym link to /tmp
  cd ~
  ln -s /tmp .android
  

Find my project on [[http://www.bitbucket.org|bitbucket.org]] and create a fork.  You will be working with your own forked (which is a clone) repository.  You will clone locally (checkout), commit (and branch if you want)  and push to your fork.  You can then request a pull and I'll merge your changes into the master.

Clone YOUR newly created fork into your home directory
  cd ~
  git clone https://bitbucket.org/tommyfun/androidmaps
  
Now fire up eclipse and import the project
  