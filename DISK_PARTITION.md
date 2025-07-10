# CREATE DISK PARTITION
Disk partitioning means dividing your physical storage device into one or more logical sections that can be treated as separate disks

**Why Partition your disk?**

- Organization: separates different type of data for better management
- Data isolation: if one partition becomes corrupted, data in other partitions may remain safe
- Flexibility: allows to use different file systems in different partitions e.g. ext4 for root, FAT32 for EFI

GPT partition table: this file will guide you to create a disk partition with the following structure:
| MOUNT POINT | FILE SYSTEM | SIZE | PURPOSE | ATTRIBUTES |
| ------------|-------------|------|---------|------------|
|/boot/efi |FAT32 |500MiB |EFI System Partition (ESP): Stores UEFI bootloaders and kernel images|boot, esp|
|/ |Ext4 |50GiB |Root Partition: Contains the operating system files |None |
|[SWAP] |Linux Swap |6GiB |Swap Space: Used for virtual memory when RAM is full or for hibernation |Swap |
|/home |Ext4 |Remainder of disk |Home Partition: Stores user files, configurations, and documents |None |

*The /home partition is optional but*

First to check your storage device's name input in terminal:
```sh
# fdisk -l
```
or to check current disk partitions besides the disk name:
```sh
# lsblk
```

<details>
  <summary>To create GPT partitions you will use the gdisk utility</summary>
  <p>gdisk is a disk management utility that allows users to create, manage, and delete standard GPT (GUID Partition Table) Unlike fdisk, which creates only MBR (Master Boot Record) partitions, gdisk creates only GPT partitions. GPT is the modern partitioning standard that supports larger disks and more partitions compared to MBR.</p>
</details>

```sh
# gdisk /dev/yourdiskname
```
Replacing `yourdiskname` with the disk name you found in the previous step with `# fdisk -l` or `lsblk`

If you want to delete your current disk partitions and data type, at the `gdisk` prompt type `# o` and confirm with `# y`. 
*If you are working with a clean disk you can skip this step*

Then, still at the `gdisk` prompt, we will create every new partition one by one:


<details>
  <summary> /boot/efi </summary>
  <p>Most common size guideline for EFI System Partition is between 100 MB to 550 MB. One of the reason behind this is it is difficult to resize later as it is the first partition on the drive. EFI partition may contain languages, fonts, BIOS firmware, other firmware related stuffs. There are some firmware/software that are installed into the the EFI partition instead of the data drive. The Arch Linux wiki recommends at least 512 MiB to avoid potential issues with some EFIs.</p>
</details>
  
Type `# n` to create a new partition

Press `enter` to accept the default partition number or write your preferred number

Press `enter` to accept the default first sector of disk and

Write `+512MiB` and press `enter` to set the last sector

Type `EF00` and press `enter`. This is the specific GUID for an EFI System Partition


<details>
  <summary> SWAP </summary>
  <p>A swap partition is a dedicated space on a disk drive that the Linux operating system uses as an extension of physical memory (RAM). When the system runs out of physical memory, it moves some data from RAM to the swap partition, allowing the system to continue functioning, albeit more slowly</p>
  <p>The recommended size of the SWAP partition depends on system specifications and planned use:

- For systems with less than 2GB of RAM, a swap partition size of twice the RAM is often recommended
- For systems with 2GB to 4GB of RAM, a swap partition size of the same as the RAM plus 2GB is suggested
- For general use is suggested that 2GB to 4GB is sufficent for the SWAP partition
- If hibernation is required it is recommended to set the SWAP partition at least as large as the amount of RAM, as the system needs to store the entire contents of RAM in the swap space when hibernating</p>
</details>

Type `# n` to create a new partition

Press `enter` to accept the default partition number or write your preferred number

Press `enter` to accept the default first sector of disk and

Write `+4GiB` and press `enter` to set the last sector. (Adjust size based on your RAM; typically RAM size or double RAM for hibernation)

Type `8200` and press `enter`. This is the GUID for a Linux swap partition


<details>
  <summary> / </summary>
  <p>The root partition, denoted as "/", is the primary partition in a Linux system that contains the entire file system structure, including system files, and program settings. Recommended size depends on the operating system, display/window manager, and applications intended to use. Generally at least 40GiB is recommended although lightweight desktop environments might function well with less space</p>
</details>

Type `# n` to create a new partition

Press `enter` to accept the default partition number or write your preferred number

Press `enter` to accept the default first sector of disk and

Write `+40GiB` and press `enter` to set the last sector. (You can choose `+20GiB` for minimal, or more like `+50GiB` for more software. Adjust as needed.)

Type `8300` and press `enter`. This is the GUID for a Linux filesystem partition (Ext4)


<details>
  <summary> /home </summary>
  <p>A /home partition is a separate section of a hard drive that stores user-specific files and settings. This includes personal documents, photos, videos, browser history, preferences, and application configurations. The /home partition is optional as many systems store this data in the root partition (/) along system files, but this separation can offer several benefits, such as easier system reinstallation, improved data safety, and better organization</p>
</details>

Type `# n` to create a new partition

Press `enter` to accept the default partition number or write your preferred number

Press `enter` to accept the default first sector of disk and

Just press `enter` to use the remainder of the disk for your home partition

Type `8300` and press `enter` to create another Ext4 partition

<p>_</p>

To see the partitions you've created, type `p` and press `enter`
