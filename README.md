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

Now you are connected to the internet your system should synchronize its clock automatically, systemd-timesyncd is enabled by default, verify that it is correct entering into the console:
```sh
# timedatectl
```
Make sure that the system clock is synchronized and the NTP service is active, this will ensure that your system maintains an accurate time and date. You can also setup your timezone in this step:
<details>
  <summary>Set your system timezone</summary>
You can see the list of timezones available with the following command:
 
 ```sh
# timedatectl list-timezones
```
Timezones are generally listed as `Continent/City` or `Country` or timezone abbreviations e.g: `GMT` you can filter the timezones you are looking for with the command:
```sh
# timedatectl list-timezones | grep Continent
``` 
to set the timezone use the command:
```sh
# timedatectl set-timezone Continent/City
```
  </p>
</details>

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

🔸[**Follow this guide**](DISK_PARTITION.md) **to setup a GPT layout with the following partitions:**
| MOUNT POINT | FILE SYSTEM | SIZE | PURPOSE | ATTRIBUTES |
| ------------|-------------|------|---------|------------|
|/boot/efi |FAT32 |500MiB |EFI System Partition (ESP): Stores UEFI bootloaders and kernel images|boot, esp|
|/ |Ext4 |50GiB |Root Partition: Contains the operating system files |None |
|[SWAP] |Linux Swap |6GiB |Swap Space: Used for virtual memory when RAM is full or for hibernation |Swap |
|/home |Ext4 |Remainder of disk |Home Partition: Stores user files, configurations, and documents |None |

After formating and mounting the newly created partitions you are ready to continue to the next step.

## PACKAGE INSTALL
- ### Select mirrors

Mirrors are servers replicating the repositories from ArchLinux. These mirrors help reduce load on the main server and provide faster access for users based on their location. You can check the current mirror list with the commad `# cat` or `# less` e.g:
```sh
# cat /etc/pacman.d/mirrorlist
```
<details>
  <summary>Tip</summary>

 To exit command `# less` type `# q`
 </details>

You can update the list of mirrors to optimize download speed. To change the mirror servers, you can either manually edit the `/etc/pacman.d/mirrorlist` file using `# nano` or `# vim` or use a tool like `reflector` as follows:
```sh
sudo reflector --protocol https --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
```
This command selects the 10 most recently synchronized HTTPS mirrors, sorts them by download speed, and saves the result to the mirror list file
If you want to limit the mirrors to specific countries, you can use the `--country` option:
```sh
sudo reflector --country "Country1,Country2" --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
```

- ### Install essential packages
> **NOTE**: No software or configuration (except for `/etc/pacman.d/mirrorlist`) gets carried over from the live environment to the installed system. Therefore is essential to install packages to start with a functional base system

