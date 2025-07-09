# CREATE DISK PARTITION
Disk partitioning means dividing your physical storage device into one or more logical sections that can be treated as separate disks

**Why Partition your disk?**

- Organization: separates different type of data for better management
- Data isolation: if one partition becomes corrupted, data in other partitions may remain safe
- Flexibility: allows to use different file systems in different partitions e.g. ext4 for root, FAT32 for EFI

GPT partition table: this file will guide you to create a disk partition with the following structure:


