# Installing Linux (in a Dell 7566)

I made this guide for myself, it is a short guide for the impatient, lazy, fool people that want dual boot nowadays.
If you are intelligent or willing to spend time and effort then you can find better guides, or you may not need one to begin with.
Initially I wanted to use the SSD for caching, and maybe that is a great idea, but it is very complex, takes time and effort.
The current goal of this guide is putting the not-so-mutable files of Linux in the SSD and everything else in the HDD.
This may not be what we need, but it is what we deserve, if you want something better, please work on it,
  and please make a guide for the rest of us, the lazy impatient idiots.
So here is how this goes:

## Getting the things that you will need

1. You will need a Windows restore pendrive, use the windows tools to create one.
   Remember that it needs to have at least 16GB in size.
   Dell is an awesome vendor that from time to time updates its computers to non-working USB drivers.
   If that is your situation, try to update your drivers to a version that works, you need 
1. You need to prepare a pendrive for the installation of Linux (I recommend Xubuntu, it doubles as a live-USB)
    1. Download your preferred Linux, or two of them if you are going to use different Live-USB and installation pendrives.
    1. Put them in the corresponding pendrives, I used rufus to do that from windows, you can use something else, e.g. `dd`
1. You will need a external HDD with as much space as your SSD (it is 128GB for my laptop)

## Moving Windows to the HDD

1. Check what is the current situation of your drives `fdisk -l`, identify which one is the SSD, I will assume it is sda (**check it**)
1. Clone your SSD to a file using `dd if=/dev/sda of=/path/to/file bs=64K conv=noerror,sync status=progress`, in the external HDD.
   This may be useful in the future. Technically you don't need to do this with the recovery options that windows has, but...
1. Boot with the LiveUSB, use gparted, create the partitions in the HDD.
   Choose your preferred size and location, I won't judge you, but for the sake of what you love most,
   align the partitions to a sector, it is a checkbox over there.
1. Move the partitions from the SSD to the HDD with `dd`.
   My suggestion is to do this one by one, and leave EFI in the SSD (normally the first one).
1. Delete the partitions that you have moved from the original SSD.
   Now you can try to boot your computer, but that won't work. Isn't it great?
   You took a perfectly working laptop and destroyed it beyond any possible repair :D
1. You have to tell EFI how to find windows again, it's _very easy_.
    1.
1. Now you should be able to boot Windows again, but we are not done yet, you need to defrag the HDD.
   This was not necessary back when it was a SSD, and now it is messy...
    1. First, Windows has to recognise it as a HDD, open `cmd` as an admin and run ``
    1. Second, you have to defrag it, just use the regular tools for that
    1. For some reason Windows works like shit and you will need to defrag it like every other day,
       if you find a solution for that, please let me know.


Useful references:

https://www.grepular.com/Disk_Caching_with_SSDs_Linux_and_Windows  
https://www.dell.com/support/article/us/en/19/SLN147923/intel-smart-response-technology-configuration?lang=EN  
https://www.howtoforge.com/tutorial/installing-tensorflow-neural-network-software-for-cpu-and-gpu-on-ubuntu-16-04/?platform=hootsuite

