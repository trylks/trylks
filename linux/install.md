# Installing Linux (in a Dell 7566)

There is a lot of info in this guide.
There is a lot of info missing in this guide.
Open an issue if you miss something particularly relevant.

## Personal prelude (I want to write it, you want to skip it)

Always a frustrating experience, sometimes you may question:
"Why installing it in the first place?"
"Aren't virtual machines good enough?".

Well, not for what I want to achieve.

* You want Docker? Then say [hello to VirtualBox](https://docs.docker.com/toolbox/toolbox_install_windows/)
* Using VirtualBox? Then forget about [proper GPU support](https://superuser.com/questions/779070/use-nvidia-gpu-from-virtualbox).
* And of course you can use bash in Windows,
   but that may [not work so well for graphical aplications](https://www.howtogeek.com/261575/how-to-run-graphical-linux-desktop-applications-from-windows-10s-bash-shell/),
   or anything interesting for the matter.

At this point Windows feels like a [tar pit](http://nowbuzz.me/wp-content/uploads/2016/10/12-PuppyInTar.jpg),
where it is [impossible to move](http://shaffner.us/cs/papers/tarpit.pdf):

* No proper support for C, nor Python, Rust is a pain, bash is hard to have...
* Software compatibility problems. E.g. not even all Atom extensions work properly, because some need access to compilers and libraries and stuff
  (this doesn't mean they do on Linux, but oftentimes they are easier to fix there).
  Using Rust in Windows is also more challenging than using it on Linux.
* Windows is especially challenging for newest frameworks and libraries, as for example TensorFlow and Docker.
* Windows is especially challenging for developer tools, like SBT.
* Aptitude.
* Ads..
* Personal preference, I like Linux.

At the same time, Linux feels like a box of [shaving blades](http://hairfreelife.com/wp-content/uploads/2015/04/feather_razor_blade_close_up.jpg).
Each step is full errors, and [each step is full of pain](https://www.youtube.com/watch?v=aRq1Ksh-32g).
Some errors were reported with UEFI when it was new, I do not even know what is the current situation now.
So I will definitively keep Windows on dual boot, to use it with hardware (I had an iPod, once time ago), and for gaming.
How will I get the time to do all these things? (including gaming!)
I have no idea, but if I do, I may write a similar guide to build desktop computers with liquid cooling also for GPU.
Reason is, if I can get everything to work and make money with this, a Desktop computer will be mandatory for me,
hopefully after I can make enough money to have a house to put it into.
Time will tell.

You may have different motivations.
Whatever your motivations are, if you have already discarded other options, then go ahead and do this.

## Objectives of this guide

Some are covered already and checked, some are not. The eternal fight with Kronos that he keeps winning...

- [x] Decide on a GNU/Linux distribution to install
- [ ] Check the compatibility with your hardware
- [ ] Make room for the installation
- [ ] Install Linux.
- [ ] Fix problems with Linux.
- [ ] Improve linux (thermal management, etc.)
- [ ] Install Tensorflow. 
- [ ] Improve user account (to git, etc.)


## Steps to perform

### Decide on the distribution that you will install

I chose Xubuntu.
Ubuntu for the compatibility with the hardware (seems to be the best distribution in this regard).
Xubuntu is just Ubuntu with the XFCE desktop environment.
I am used to it and I like it much, so I will be using XFCE.
In addition, I do not want to consume resources in the GPU with the desktop environment,
  to use Deep Learning and possibly more number crunching.
YMMV wrt hardware compatibility or preferences.

### Check compability with your hardware

If this doesn't work fine, go back to previous step.
Easiest way to go is using a bootable USB stick with your distro, if live mode is supported (booting from a USB).

If you are on Windows:
You can write them with [Rufus](https://rufus.akeo.ie/), or [Win32DiskImager](https://wiki.ubuntu.com/Win32DiskImager).

If you are on Linux then the usual `dd` command. 

There is plenty of info online about how to do this.
What is more rare to find is a list of the things that you should be checking:

- [x] The system boots (hard to miss, though)
- [ ] HDD is accessible, absolutely necessary to be able to install Linux in it.
- [x] Internet connection, important because the installation will probably get packages from the Internet.
- [ ] CPU and GPU(s) are recognized properly. Patching the kernel later may be needed/feasible, but that's another guide.
- [ ] Graphics, sound, multimedia, bluetooth, other devices... These are secondary, and AFAIK the risk of needing to patch the kernel is lower.

Patching the kernel is not a tragedy, but I've never needed to do it, and if possible I would like to keep that so.
[Life is short](https://quotefancy.com/quote/767996/Arthur-Schopenhauer-If-a-man-wants-to-read-good-books-he-must-make-a-point-of-avoiding).
And I would enjoy to have some spare time for video games...

### Make room for the installation

If you already have Windows and what not on your computer, you will need to create partitions on it, and hope everything goes well.

1. Create a [USB recovery drive](https://support.microsoft.com/en-us/instantanswers/3a747883-b706-43a5-a286-9e98f886d490/create-a-recovery-drive) if you don't have one
1. Have some backup (out of the PC) for all your files, configuration, and whatever you may need.
1. Reshape partitions with some software appropriate for it (I've used [partition magic](http://www.partition-tool.com/partition-magic/free-download-partition-magic-full-version.html) in the past)
1. Leave some empty partitions.

In particular, for Dell 7566 as it comes preconfigured, partition magic is not necessary.
It has a 128GB SSD and a 1TB HDD.
Initially, everything is in the 128GB SSD.
Steps for my preferred configuration are:

1. Split HDD in two partitions 400GB for Linux, not partitioned (free space) remaining for Windows.
   Reason is: Windows is accessible from Linux, so all multimedia and games goes there.
   Datasets and large files may also go there, at least old ones.
   If you are worried about Windows partition not being accesible because NTFS locks it when hibernating, then split it in three.
        
      1. Windows for hibernation, installing software, etc. about 300GB IMHO
      2. Another one for Linux, about 400GB
      3. Remaining for shared files.
1. Clone SDD in Windows HDD partition.
   Check it is fine.
   It should boot, if it doesn't, it is not properly recognized as a bootable OS.
   You have several options:
   
      1. Try to fix it yourself. Good luck!
      1. Use Grub and hope it fixes your installation. (I chose this, I'm lazy).
      1. Repeat with [better software](http://windowsreport.com/move-windows-10-ssd/).
         If it is possible to move to the SSD, it must be possible to move _from_ the SSD.
         I mean, [shouldn't the reverse be possible?](https://youtu.be/9DLQyo3NntE?t=1m11s)
1. Check the boot order is correct, try to boot from the "new" windows.
1. Partition SSD, deleting Windows. One partition goes to Windows, the other one to Linux.
   Make both partitions slightly uneven, that may make identification easier if needed.
   Bigger for Linux or Windows that's entierly for you.
1. For Windows smart response from Intel should work fine.
   It is a [no-go for Linux](http://askubuntu.com/questions/308481/howto-run-ubuntu-with-uefi-and-intel-smart-response-technology), though.
   On Linux it is possible to use [flashcache or bcache](http://askubuntu.com/questions/252140/how-do-i-install-and-use-flashcache-bcache-to-cache-hdd-to-ssd).
   Another nice option is to use the SSD for the swap partition.
   It depends on what annoys you more:
     a. Time to hibernate and wake up Linux, or running out of memory (e.g. you are doing big data)
     b. Time to boot up usual programs, like the web browser, your favorite IDE, etc.
   The latter is worse IMHO, so I'm going with this one, if suitable during installation...
1. Install Linux, hope everything goes well. (This includes the flashcache, bcache, or swap installation properly)
1. Check that the Linux installation works as expected.
1. Install SRT for faster Windows too.

#### Install SRT

http://www.pcworld.com/article/248828/how_to_setup_intel_smart_response_ssd_caching_technology.html
http://askubuntu.com/questions/308481/howto-run-ubuntu-with-uefi-and-intel-smart-response-technology

#### Install bcachefs

https://bcache.evilpiepirate.org/
http://askubuntu.com/questions/252140/how-do-i-install-and-use-flashcache-bcache-to-cache-hdd-to-ssd

### Improve Linux

Power management.
https://help.ubuntu.com/community/PowerManagement/ReducedPower
https://help.ubuntu.com/community/SensorInstallHowto

Check the usage of the CPU, the integrated GPU, and the Nvidia GPU.

The clock speed of the processor should be variable according to the usage too.



### Install Tensorflow

There are very nice guides online, I do not plan to replicate them, but to fill any gaps that they may have.
For example being more customized to Dell 7566.



# When everything fails

1. [Install tensorflow with GPU support for windows](https://nitishmutha.github.io/tensorflow/2017/01/22/TensorFlow-with-gpu-for-windows.html)
2. Play "In the end" by Linkin Park...



