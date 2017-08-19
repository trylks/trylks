# Installing Linux (in a Dell 7566)

I made this guide for myself, it is not ready to be consumed by anyone that is not me.
[Previous version](https://github.com/trylks/trylks/blob/4d1fd6338b37d0fc67493064f885e8576a8b1913/linux/install.md).

## TODO

Useful references:

https://www.grepular.com/Disk_Caching_with_SSDs_Linux_and_Windows
https://www.dell.com/support/article/us/en/19/SLN147923/intel-smart-response-technology-configuration?lang=EN
http://www.linuxquestions.org/questions/linux-newbie-8/gparted-partitioning-options-align-to-cylinder-or-to-mib-834366/
https://www.thomas-krenn.com/en/wiki/Partition_Alignment
https://www.howtoforge.com/tutorial/installing-tensorflow-neural-network-software-for-cpu-and-gpu-on-ubuntu-16-04/?platform=hootsuite

LVM better than bcache?

## Personal prelude (I want to write it, you want to skip it)

## Objectives of this guide

Some are covered already and checked, some are not. The eternal fight with Kronos that he keeps winning...

- [ ] Move Windows (try to make it work after moving)
- [ ] Install Linux (Xubuntu LTS is usually an easy option) (Grub could fix Windows)
- [ ] Improve Linux (thermal management, etc.)
- [ ] Install some Deep Learning library that uses the GPU.
- [ ] Customize and save customization to reuse


## Steps to perform

### Move Windows

1. Create a [USB recovery drive](https://support.microsoft.com/en-us/instantanswers/3a747883-b706-43a5-a286-9e98f886d490/create-a-recovery-drive) and data backup, because shit happens, it's always good having one.
1. [Rearrange partitions](http://lifehacker.com/5837543/how-to-migrate-to-a-solid-state-drive-without-reinstalling-windows),
[with partition magic?](http://www.partition-tool.com/partition-magic/free-download-partition-magic-full-version.html).
Leaving empty space is the best way for Ubuntu to find such space.
Here is the original config:

      Disk /dev/sda: 119.2 GiB, 128035676160 bytes, 250069680 sectors
      Units: sectors of 1 * 512 = 512 bytes
      Sector size (logical/physical): 512 bytes / 512 bytes
      I/O size (minimum/optimal): 512 bytes / 512 bytes
      Disklabel type: gpt
      Disk identifier: C5B1902E-21A8-4656-B516-E141380BB171

      Device         Start       End   Sectors   Size Type
      /dev/sda1       2048   1026047   1024000   500M EFI System
      /dev/sda2    1026048   1288191    262144   128M Microsoft reserved
      /dev/sda3    1288192 223821823 222533632 106.1G Microsoft basic data
      /dev/sda4  223821824 224743423    921600   450M Windows recovery environment
      /dev/sda5  224743424 247797759  23054336    11G Windows recovery environment
      /dev/sda6  247799808 250068991   2269184   1.1G Windows recovery environment


      Disk /dev/sdb: 931.5 GiB, 1000204886016 bytes, 1953525168 sectors
      Units: sectors of 1 * 512 = 512 bytes
      Sector size (logical/physical): 512 bytes / 4096 bytes
      I/O size (minimum/optimal): 4096 bytes / 4096 bytes
      Disklabel type: gpt
      Disk identifier: BB8208FC-D09B-4909-929A-7B5A0B4C22F5

      Device      Start        End    Sectors   Size Type
      /dev/sdb1    2048     264191     262144   128M Microsoft reserved
      /dev/sdb2  264192 1953523711 1953259520 931.4G Microsoft basic data
1. Next config:
    Split HDD in two partitions 400GB for Linux, not partitioned (free space) remaining for Windows.
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

### Get Deep Learning to work on GPU


