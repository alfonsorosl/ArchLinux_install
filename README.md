# ArchLinux_install
This install of Arch follows instructions from the Installation guide from the ArchWiki, it will only dig deeper in specific steps to expand the understanding of the installation and to help learn more details about the system being installed

<p align="center">
 <img src="https://github.com/user-attachments/assets/adaf12c2-7bf3-4553-ab7f-f57b1d6c43d8" alt="alt text" width="750" height="422">

Welcome to this simple guide to install Arch Linux

*Recomendation: follow the official installation guide from the Archwiki > https://wiki.archlinux.org/title/Installation_guide#*

## PRE-INSTALL
- Download the ArchLinux image > https://archlinux.org/download/

- Optional: you can verify the signature followinf the instructions in the ArchWiki

- Format an USB stick to FAT32 and use a tool like rufus to make the image bootable

- Boot your machine

*Tip: if you need more assistance with these steps, the ArchWiki has a more detailed description, use external guides and tutorials specific to your hardware characteristics for a better explanation*

## SYSTEM PREP
Welcome to the ArchLinux installation console, select the install medium and you will be presented with a virtual console as the root user with a zsh shell prompt. The console will have by default the US keymap and the packages listed in this file https://geo.mirror.pkgbuild.com/iso/latest/arch/pkglist.x86_64.txt will be available. *To change the keymap to match your keyboard or change to different console, refer to the ArchWiki*

You will setup Arch now:
- ### Connect to internet

  First step is to connect to the internet

  - Ethernet > plug the cable and check connection with ping
  - Wifi > 🔸follow instructions to authenticate wireless network with iwctl https://wiki.archlinux.org/title/Iwctl

Now you are connected to the internet your system should synchronize its clock, verify that it is correct entering into the console:
```sh
# timedatectl
```

- ### Partition the disk

Next you will partition your disk, check what disks your system has available with the command:
```sh
# fdisk -l
```
<details>
  <summary>Ignore rom, loop or airootfs - rpmb, boot0 and boot1</summary>
  <p>You may ignore "rom" refers to read-only memory, "loop" devices are virtual block devices used for mounting files as if they were physical disks, and "airootfs" is a temporary root file system used during the installation of Arch Linux.</p>
  <p>For mmcblk devices you may ignore "rpmb" is a small partition that can only be accessed via a trusted mechanism. It is used for secure storage, "boot0" and "boot1" are hardware partitions used for the boot process.</p>
</details>

To partition we need to choose a configuration MBR or GPT. 
- **MBR** usually used in BIOS systems
  - Stores partition information in its first sector, limited to 4 primary partitions and managing disks up to 2 TB
  - The MBR also contains the initial bootloader code, which points to the operating system's boot sector

- **GPT** common in more modern UEFI firmware
  - Stores robust partition data with redundancy (copies at both ends of the disk), supporting virtually unlimited partitions and disks of massive sizes (beyond 2 TB)
  - It uses globally unique identifiers (GUIDs) for partitions and stores direct pointers to UEFI bootloader files on a dedicated EFI System Partition (ESP)

🔸**SCRIPT FOLLOW THIS GUIDE to setup a GPT layout with the following partitions:**
| MOUNT POINT | FILE SYSTEM | SIZE | PURPOSE | ATTRIBUTES |
| ------------|-------------|------|---------|------------|
|/boot/efi |FAT32 |500MiB |EFI System Partition (ESP): Stores UEFI bootloaders and kernel images|boot, esp|
|/ |Ext4 |50GiB |Root Partition: Contains the operating system files |None |
|[SWAP] |Linux Swap |6GiB |Swap Space: Used for virtual memory when RAM is full or for hibernation |Swap |
|/home |Ext4 |Remainder of disk |Home Partition: Stores user files, configurations, and documents |None |

After partitioning the disk you will format all disk partitions using each command accordingly:
```sh
# mkfs.ext4 /dev/sdaX
# mkswap /dev/sdaX
# mkfs.fat -F 32 /dev/sdaX
```

And mount the disk partitions using each command accordingly:
```sh
# mount /dev/sdaX /mnt
# mount --mkdir /dev/sdaX /mnt/boot
# swapon /dev/sdaX
```
> **WARNING**: *Only format the EFI system partition if you created it during the partitioning step. If there already was an EFI system partition on disk beforehand, reformatting it can destroy the boot loaders of other installed operating systems.*
