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
    1. Put them in the corresponding pendrives.
        1. I used [rufus](https://rufus.akeo.ie/) to do that from Windows, it is a good option.
        1. If you do not want to use rufus, there are plenty of options, including `dd` (we are going to get very familiar with it)
1. You will need a external HDD with as much space as your SSD (it is 128GB for my laptop)

## Moving Windows to the HDD

1. Check what is the current situation of your drives `fdisk -l`, identify which one is the SSD, I will assume it is sda (**check it**)
1. Clone your SSD to a file using `dd if=/dev/sda of=/path/to/file bs=64K conv=noerror,sync status=progress`, in the external HDD.
   This may be useful in the future.
   Technically you don't need to do this with the recovery options that windows has, but...
1. Since you are at it, clone the restoration pendrive too.
1. Boot with the LiveUSB, use gparted, create the partitions in the HDD.
   Choose your preferred size and location, I won't judge you.
1. Once you are done, check with `fdisk -l` that partitions are aligned properly.
   In theory using `align to cylinder` in gparted should provide the best results.
   In practice I had better results using `align to MiB`, so... good luck!
   Just make sure that everything is working properly before continuing with the next steps, this is important.
1. Move the partitions from the SSD to the HDD with `dd`, just like before, with proper numbers and all.
   My suggestion is to do this one by one, and leave EFI in the SSD (normally the first one).
1. Delete the partitions that you have moved from the original SSD.
   Now you can try to boot your computer, but that won't work. Isn't it great?
   You took a perfectly working laptop and destroyed it beyond any possible repair :D
1. You need to defrag the HDD, this was not necessary back when it was a SSD, and now it is messy... 
   You can do it now (from Linux) or later (from Windows), I will remind you later anyway.
   Doing it from Linux is "[not recommended](https://bbs.archlinux.org/viewtopic.php?id=125529)".
   Since we are going to fix the boot soon, there should be no problem, but run this at your own risk, or don't.
   To defrag it directly from your Live-USB, do something like: `fsck -t ntfs --kerneldefrag /dev/sdX`.
   If that does not work, maybe that was for the better.
1. You can safely delete the files at `C:\Windows\Temp`, do it while on Linux, they are not being used by any process.
1. OK, now it is time to repair your Windows.
   You have to tell EFI how to find Windows again, it's _very easy_.
    1. Boot the computer from the restoration USB; select repair > operating system > command prompt
    1. Run `diskpart`, use `list vol` to see the volumes, play Hanoi towers to get `C:` to be `C:`, the commands are:
        1. `sel vol $number` to select some volume
        1. `assign letter=$letter:` to assign a letter
    1. Remember to assign a letter to the EFI partitition, e.g. A, you will need that later, then exit with `exit` (that unexpected)
    1. Let's say the EFI partition is `A:`, then do `cd /d A:\EFI\Microsoft\Boot\`, to be in the right place at the right time
    1. Do `bootrec /FixBoot`, this probably wasn't necessary, but better be safe
    1. Then, since you are here, save the current boot just in case: `ren BCD BCD.old`
    1. And make the new one: `bcdboot C:\Windows`
1. Now you should be able to boot Windows again, but we are not done yet,
    1. First, Windows has to recognise the HDD it is installed into as an HDD, open `cmd` as an admin and run `winsat formal`.
    1. If it is still recognised as a SSD and you cannot optimise it, run `winsat diskformal`.
    1. Now Windows will handle the defrag for you in the future, but you may want to defrag it manually now and then anyway.
    1. If you didn't defrag the HDD earlier (you shouldn't) do it now using the tools at your disposal in Windows.
    1. Check the size of the Windows partition and if it is right.
1. If the size of the windows partition is not right you can
   [extend](https://serverfault.com/questions/90870/i-have-a-ntfs-partition-now-the-size-of-the-partition-seems-to-be-wrong-is-ther)
   it booting from the repair pendrive again.
1. Before continuing, check that everything in your Windows installation is working properly, if it doesn't you can always:
    1. Format the HDD
    1. Restore the SSD from the file you saved on step 2.
    1. Repeat until insanity from step 3, because you wanted to do this, didn't you?
    
## Install GNU/Linux

1. You should have some spare space in both the SSD and the HDD.
   This would be a good moment to create the swap partition in the HDD.
1. Get your favourite distribution and flavor in a pendrive (I did this with rufus)
1. For some distributions you may need to disable secure boot in the UEFI configuration.
   In Dell this is done pressing F12, then navigating the menus.
1. Install it fully in the SSD (this is distribution dependent, but most make it really easy with a graphical installer)
1. Normally Linux uses global time and windows uses local time, which causes a lot of trouble when using dual boot.
   Either:
    1. [Configure Windows to use global time](https://lifehacker.com/5742148/fix-windows-clock-issues-when-dual-booting-with-os-x).
    1. [Configure Linux to use local time](https://askubuntu.com/questions/169376/clock-time-is-off-on-dual-boot).
       It is as simple as `timedatectl set-local-rtc 1`.
    1. Accept that time may be several hours off sometimes (the minutes are fine).
    If you don't switch between windows and linux very often, this is an acceptable (and very lazy) solution,
    they automatically set the correct time and keep it until you switch to the other OS.
1. Move some paths from the SSD to the HDD:
    1. Identify the partition where you want to put the stuff, it will probably be `/dev/sdbX`.
    1. Then use `blkid` or if it fails `vol_id -u /dev/sdbX` to find the ID of the partition we are considering.
    1. backup fstab then `vim /etc/fstab` to mount the drive, with a line like this: `UUID=$ID   /.hdd   ext4   defaults  0  2`
    1. create the paths in `/.hdd`, I would choose `home opt root tmp var`
    1. copy the files from the SDD to the HDD with rsync preserving permissions and all stuff
       like this: `rsync -axHAWX --info=progress2 $orig $dest`
    1. Check that the copy went well: `diff -r $orig $dest -x ".gvfs/*"`
    1. experiment a bit with `mount -o bind $orig $dest`, alternatively `mount -B $orig $dest` before persisting the changes in fstab
    1. Edit fstab, `/path/orig /new/path bind defaults 0 0` is the syntax to bind in [fstab](https://help.ubuntu.com/community/Fstab).
       Basically the syntax is: `[Device] [Mount Point] [File System Type] [Options] [Dump] [Pass]`.
    1. it's best to mount old paths in a new one, and then the new one in the old one, so as to delete the contents of old paths,
       but at this point it is enough with moving all paths to `/old_$path` (to have a copy in case something goes wrong),
       and then rebooting or mounting all: `mount -a`
    1. If you screwed things up, try to repair them with the live USB.

## Bonus: Use the GPU for calculation
Now we are going to check that TensorFlow and Pytorch use the GPU correctly and that it is not overused and stays cool
(optimus, bumblebee, ...).
We will also check that TensorFlow and PyTorch, so first we will install Python, Python environments, 
TensorFlow, PyTorch, and a few other things.
TensorFlow makes an excellent description of the [requirements](https://www.tensorflow.org/install/install_linux) to install before.

1. Install python if it is not installed yet.
1. Install [pipenv](https://pypi.python.org/pypi/pipenv) it is the least insane option for python projects nowadays.
1. 

Some references (I had too many tabs open at the same time):

1. http://www.dell.com/support/article/ie/en/iedhs1/sln300987/how-to-repair-the-efi-bootloader-on-a-gpt-hdd-for-windows-7--8--81-and-10-on-your-dell-pc?lang=en
1. https://superuser.com/questions/1006877/windows-10-optimize-drives-shows-ssd-as-hard-disk-drive
1. https://askubuntu.com/questions/169376/clock-time-is-off-on-dual-boot
1. https://www.drivereasy.com/knowledge/fix-100-disk-usage-in-task-manager-improve-pc-performance-on-windows-10/#d
1. https://support.microsoft.com/en-us/help/832316/the-partition-size-is-extended-but-the-file-system-remains-the-origina
1. https://serverfault.com/questions/90870/i-have-a-ntfs-partition-now-the-size-of-the-partition-seems-to-be-wrong-is-ther
1. http://priede.bf.lu.lv/ftp/pub/Utilities/DiskDefrag/Defragment_NTFS_in_Linux.html
1. https://help.ubuntu.com/community/Partitioning/Home/Moving
1. https://help.ubuntu.com/community/MoveMountpointHowto
1. https://serverfault.com/questions/67038/how-do-i-mount-sub-directory-to-a-hard-drive-in-linux
1. https://www.reddit.com/r/archlinux/comments/6vmxcb/repair_efi_before_installing_for_dual_boot/
1. https://www.reddit.com/r/linuxquestions/comments/75lin2/using_ssd_for_hdd_cache_in_a_dualboot_ubuntu/
1. http://www.linux-magazine.com/Online/Features/Tune-Your-Hard-Disk-with-hdparm
1. https://catdevblog.nickbair.net/2010/10/30/a-good-ssdhdd-partitioning-scheme/
