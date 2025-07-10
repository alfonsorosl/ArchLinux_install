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


```sh
# gdisk /dev/yourdiskname
```
Replacing `yourdiskname` with the disk name you found in the previous step with `# fdisk -l` or `lsblk`

If you want to delete your current disk partitions and data type, at the `gdisk` prompt type `# o` and confirm with `# y`. 
*If you are working with a clean disk you can skip this step*

Then, still at the `gdisk` prompt, we will create every new partition one by one:
gdisk is a disk management utility that allows users to create, manage, and delete standard GPT (GUID Partition Table) Unlike fdisk, which creates only MBR (Master Boot Record) partitions, gdisk creates only GPT partitions. GPT is the modern partitioning standard that supports larger disks and more partitions compared to MBR

**/boot/efi**

Type `# n` to create a new partition

**SWAP**

**/**

**/home**
