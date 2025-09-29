# CREATE MBR DISK PARTITION

MBR partition table: this file will guide you to create a disk partition with the following structure:
| MOUNT POINT | FILE SYSTEM | SIZE | PURPOSE | ATTRIBUTES |
| ------------|-------------|------|---------|------------|
|/ |Ext4 |50GiB |Root Partition: Contains the operating system files, including /boot |Bootable Flag |
|[SWAP] |Linux Swap |6GiB |Swap Space: Used for virtual memory when RAM is full or for hibernation |Swap |
|/home |Ext4 |Remainder of disk |Home Partition: Stores user files, configurations, and documents |None |

The /home partition is optional but recommended for modularity

First to check your storage device's name input in terminal:
```Bash
$ lsblk -f
```
Create disk partitions

<details>
<summary>To create MBR partitions you will use the fdisk utility</summary>
<p>fdisk is a disk management utility that allows users to create, manage, and delete standard MBR (Master Boot Record) partitions. MBR is the older partitioning standard, limited to 4 primary partitions and typically used for Legacy BIOS booting. Unlike gdisk, which creates only GPT partitions, fdisk is the classic tool for MBR.</p>
</details>

```Bash
$ fdisk /dev/yourdiskname
```
Replacing yourdiskname with the disk name you found in the previous step with $ lsblk or fdisk -l

If you want to delete your current disk partitions and data type, at the fdisk prompt type $ o and press enter. This will create a new, empty DOS (MBR) partition table.
If you are working with a clean disk you can skip this step

Then, still at the fdisk prompt, we will create every new partition one by one:

<details>
<summary> / </summary>
<p>The root partition, denoted as "/", is the primary partition in a Linux system that contains the entire file system structure, including system files, program settings, and the boot directory (which holds the kernel and bootloader files for BIOS booting). Recommended size depends on the operating system, display/window manager, and applications intended to use. Generally at least 40GiB is recommended although lightweight desktop environments might function well with less space</p>
</details>

Type $ n to create a new partition

Type $ p for Primary partition

Press enter to accept the default partition number (1)

Press enter to accept the default first sector of disk and

Write $ +50GiB and press enter to set the last sector. (Adjust size as needed.)

Type $ t to change the partition type ID.

Type $ 1 to select partition 1.

Type $ 83 and press enter. This is the MBR code for a Linux filesystem partition (Ext4).

Type $ a and press enter to toggle the bootable flag on partition 1. (Essential for Legacy BIOS boot)

<details>
<summary> SWAP </summary>
<p>A swap partition is a dedicated space on a disk drive that the Linux operating system uses as an extension of physical memory (RAM). When the system runs out of physical memory, it moves some data from RAM to the swap partition, allowing the system to continue functioning, albeit more slowly</p>
<p>The recommended size of the SWAP partition depends on system specifications and planned use:

    For systems with less than 2GB of RAM, a swap partition size of twice the RAM is often recommended

    For systems with 2GB to 4GB of RAM, a swap partition size of the same as the RAM plus 2GB is suggested

    For general use is suggested that 2GB to 4GB is sufficent for the SWAP partition

    If hibernation is required it is recommended to set the SWAP partition at least as large as the amount of RAM, as the system needs to store the entire contents of RAM in the swap space when hibernating</p>

</details>

Type $ n to create a new partition

Type $ p for Primary partition

Press enter to accept the default partition number (2)

Press enter to accept the default first sector of disk and

Write $ +6GiB and press enter to set the last sector. (Adjust size based on your RAM; typically RAM size or double RAM for hibernation)

Type $ t to change the partition type ID.

Type $ 2 to select partition 2.

Type $ 82 and press enter. This is the MBR code for a Linux swap partition.

<details>
<summary> /home </summary>
<p>A /home partition is a separate section of a hard drive that stores user-specific files and settings. This includes personal documents, photos, videos, browser history, preferences, and application configurations. The /home partition is optional as many systems store this data in the root partition (/) along system files, but this separation can offer several benefits, such as easier system reinstallation, improved data safety, and better organization</p>
</details>

Type $ n to create a new partition

Type $ p for Primary partition

Press enter to accept the default partition number (3)

Press enter to accept the default first sector of disk and

Just press enter to use the remainder of the disk for your home partition

Type $ t to change the partition type ID.

Type $ 3 to select partition 3.

Type $ 83 and press enter. This is the MBR code for a Linux filesystem partition (Ext4).

<p>_</p>

To see the partitions you've created, type $ p and press enter

If the partitions are correct exit the fdisk prompt $ w and press enter to exit and save fdisk configuration

You can check again your disk partitions names with the command $ lsblk or $ fdisk -l

Format disk partitions

After partitioning the disk you will format all disk partitions using each command accordingly:

|Use | For |
---------|----------------
|mkfs.ext4 | "/" and "/home" |
|mkswap | SWAP |
Bash

$ mkfs.ext4 /dev/yourpartitionname1
$ mkswap /dev/yourpartitionname2
$ mkfs.ext4 /dev/yourpartitionname3

Mount disk partitions

And mount the disk partitions using each command accordingly:

    NOTE: mount the root parition / first as all of the other partitions will be mounted on that / partition

|Use | For |
---------|----------------
|mount, /mnt | "/" |
|swapon | SWAP |
|mount --mkdir, /mnt/home | /home |
Bash

$ mount /dev/yourpartitionname1 /mnt
$ swapon /dev/yourpartitionname2
$ mount --mkdir /dev/yourpartitionname3 /mnt/home
