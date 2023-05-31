# Assumptions
1.  The user is starting from Windows
2.  The user wants to dual boot and has enough space to accomodate a second OS.

# Setup
Get an Arch ISO from the [wiki](https://wiki.archlinux.org/title/Installation_guide). Then install [GnuPG](https://gnupg.org/download/), and verify the signature of the ISO file by using the powershell and then using the command ```gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig```. After verifying the ISO, flash a USB with the ISO using a tool like [Balena Etcher](https://etcher.balena.io/).

Also ensure that when using the Windows Disk Manager utility, there is a decent chunk of *FREE* unallocated space in the drive you want to install Arch on. Also before you do ANY partitioning, make sure you back up any sensitive and/or important files on another drive, USB or the Cloud.

After successfully flashing the USB, boot into it via your motherboard's BIOS. Restart your computer and press a key like F-11 to boot into the BIOS or select Installation Media. After booting into a successfull live USB, and waiting for the initialization, you should be greeted by a `Welcome to Arch Linux!` message, with the ability to use a terminal.

First use `efivar -l` to verify that you're in UEFI boot mode, it should spew back a lot of text, which is a good sign. Assuming you are using ethernet, use `ping www.yahoo.com` to ping yahoo which will verify that you're successfully connected to the internet. By default the ethernet setup will work, solutions like Wi-Fi or Bluetooth may not work automatically. If you're using wifi you may use the command `iwctl` to autheticate your WiFi- service.

## Partitioning and a few things

After verifying a working internet connection, use `lsblk` to list all the connected disks and proper partitions on your computer. If you cannot tell immediately by size which drive you'd like to install Arch on, you may use the command `hdparm -i /dev/[drive]` which will return a list of availble information on the drive such as manufacturer and model. 

### Zapping Drive [OPTIONAL]
Zapping your drive will wipe all the data from the drive, and is necessary if you want to dedicate an entire drive to Arch, if not, you may parition the free allocated space within Windows and skip this step. To zap, we will use gdisk. To begin enter the command `gdisk /dev/[drive]`, enter `x` for expert mode, and then `z` to zap the drive, make sure you read all the prompts and answer if you agree with what they are stating (They're asking for confirmation about wiping the drive).

### Understanding the partitioning system
There will be three partitions that you will create in order to use Arch Linux. There partitions are:
1.  EFI System Partition
2.  Swap Partition
3.  Root Partition
4.  Home partition

A quick overview of what these three mean and why there necessary goes as follows. The EFI System partition is a necessary parition that holds many applications and utilities necessary for UEFI boot, such as a boot loader. The SWAP Partition is the space that Arch will use for Swap Space. Swap space is similar to the Paging system in Windows in that when (or if) the system runs out of RAM it will write the data and memory of unimportant and old processes to a file using hard disk space rather than RAM. The Root partition will be where you install the OS (Arch itself). And lastly, the Home partition will bne the area where your personal files, installed programs and other things go (Essentially the linux filesystem partition).

### Starting

We will partition using cgdisk. Before starting the cgdisk utility, keep this table in mind, it has the type of space created by cgdisk and their respective codes, as well as recommended size, this will be useful when we begin the actual partitioning of the drive.

| Partiton Name/Type      | Code | Recommended Size |
| ----------- | ----------- | ---------------- |
| EFI System Parition (Boot) | EF00 | 1024MiB |
| Swap Partition | 8200 | 8GiB or 16GiB  |
| Root Partition | 8300 | 20GiB or 35GiB |
| Home Partition | 8300 | N/A |

To begin using cgdisk, use the command `cgdisk /dev/[drive]`. If you do not remember the name of the drive, run `lsblk` and find the name of the drive in the first column. We will beging partitioning in the order of the table given above. 

**EFI System Partition**: Select the large pool of free space and hit enter, then hit enter when asked for first sector, then enter the recommended space for the partition from the table. When asked for the code enter `EF00`.

**Swap Partition**: After creating the EFI System Partition there should be three entries in cgdisk, one for the EFI partition, free space with the remaining free space of our drive, as well as another free space partition with about 1017KiB of space allocated. The 1017KiB partition is *likely* protective MBR space which defines the location of the kernel. Select the free space partition with the rest of your hard drives space and create a new partition. Once again, hit enter when asked for the first sector, then enter the recommended space for the partition from the table. Since this is a swap partition, the code will be 8200. 

**Root Partition**: Select the large pool of free space and hit enter, then hit enter when asked for first sector, then enter the recommended space for the partition from the table. When asked for the code enter `8300`. 


# Notes

Much of the information here is gathered from the [Arch Wiki](https://wiki.archlinux.org/title/Installation_guide), and a few install videos namely [SOG's Arch Build](https://www.youtube.com/watch?v=M_f8pnXIrF8&t=484s), with a sprinkle of my own experience using Arch. This was made solely to be a digestable walkthrough that I could follow in order to help friends install Arch, THIS IS NOT TO BE USED AS A SUBSTITUTE FOR THE ARCH WIKI.
