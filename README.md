# ArchLinux_install
To install Arch follow instructions from the Installation guide from the ArchWiki, this guide will only dig deeper in specific steps to expand the understanding of the installation and to help learn more details about the system being installed

<div align="center">
        
<pre>

-`
.o+`
`ooo/
`+oooo:
`+oooooo:
-+oooooo+:
`/:-:++oooo+:
`/++++/+++++++:
`/++++++++++++++:
`/+++ooooooooooooo/`
./ooosssso++osssssso+`
.oossssso-````/ossssss+`
-osssssso.      :ssssssso.
:osssssss/        osssso+++.
/ossssssss/        +ssssooo/-
`/ossssso+/:-        -:/+osssso+-
`+sso+:-`                 `.-/+oso:
`++:.                           `-/+/
.`                                 `/

</pre>

</div>
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
$ timedatectl
```
Make sure that the system clock is synchronized and the NTP service is active, this will ensure that your system maintains an accurate time and date. You will setup your timezone later during the system configuration:

- ### Partition the disk

Next you will partition your disk, check what disks your system has available with the command:
```sh
$ lsblk -f
```
<details>
  <summary>Ignore rom, loop or airootfs - rpmb, boot0 and boot1</summary>
  <p>You may ignore "rom" refers to read-only memory, "loop" devices are virtual block devices used for mounting files as if they were physical disks, and "airootfs" is a temporary root file system used during the installation of Arch Linux.</p>
  <p>For mmcblk devices you may ignore "rpmb" is a small partition that can only be accessed via a trusted mechanism. It is used for secure storage, "boot0" and "boot1" are hardware partitions used for the boot process.</p>
</details>

Disk partitioning means dividing your physical storage device into one or more logical sections that can be treated as separate disks

Why Partition your disk?
- Organization: separates different type of data for better management
- Data isolation: if one partition becomes corrupted, data in other partitions may remain safe
- Flexibility: allows to use different file systems in different partitions e.g. ext4 for root, FAT32 for EFI
- Modularity: facilitates backing-up and reinstating your /home directories in with a different operating system

To partition we need to choose a configuration MBR or GPT. 
- **MBR** usually used in BIOS systems
  - Stores partition information in its first sector, limited to 4 primary partitions and managing disks up to 2 TB
  - The MBR also contains the initial bootloader code, which points to the operating system's boot sector

🔸[**Follow this guide**](MBR_DISK_PARTITION.md) **to setup a MBR layout**

- **GPT** common in more modern UEFI firmware
  - Stores robust partition data with redundancy (copies at both ends of the disk), supporting virtually unlimited partitions and disks of massive sizes (beyond 2 TB)
  - It uses globally unique identifiers (GUIDs) for partitions and stores direct pointers to UEFI bootloader files on a dedicated EFI System Partition (ESP)

🔸[**Follow this guide**](GPT_DISK_PARTITION.md) **to setup a GPT layout**

After formating and mounting the newly created partitions you are ready to continue to the next step.

## PACKAGE INSTALL
- ### Select mirrors

Mirrors are servers replicating the repositories from ArchLinux. These mirrors help reduce load on the main server and provide faster access for users based on their location. You can check the current mirror list with `cat` or `less` e.g:
```sh
$ cat /etc/pacman.d/mirrorlist
```
<details>
  <summary>Tip</summary>

 To exit `less` type `$ q`
 </details>

You can update the list of mirrors to optimize download speed. To change the mirror servers, you can either manually edit the `/etc/pacman.d/mirrorlist` file using a text editor like `nano` or `vim` or use a tool like `reflector` as follows:
```sh
$ sudo reflector --protocol https --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
```
The previous command selects the 10 most recently synchronized HTTPS mirrors, sorts them by download speed, and saves the result to the mirror list file.
If you want to limit the mirrors to specific countries, you can use the `--country` option:
```sh
$ sudo reflector --country "Country1,Country2" --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
```

- ### Install essential packages
> **NOTE**: No software or configuration (except for `/etc/pacman.d/mirrorlist`) gets carried over from the live environment to the installed system. Therefore is essential to install packages to start with a functional base system

There will be a minimum of packages that will be required to have a working system, the recommended packages in the [ArchWiki](https://wiki.archlinux.org/title/Installation_guide#Install_essential_packages) are base, linux, and linux-firmware:

- base: Is a package group that provides the fundamental command-line utilities, you can check the list of packages included [here](https://archlinux.org/packages/core/any/base/)
- linux: Is the generic Linux kernel
- linux-firmware: Contains essential binary firmware blobs for various hardware, including many Wi-Fi cards, GPUs, and other devices. This is crucial for getting many components working, especially Wi-Fi

Additionally these packages can also be considered essential for the system to work from the start: network-manager, grub, nano, and sudo:

- network-manager: Provides detection and configuration for systems to automatically connect to networks, supporting both wireless and wired networks
- grub: (Grand Unified Bootloader) It allows users to select a default operating system, set a background image, and control the boot process
- nano: Is a text editor used for editing files in the terminal
- sudo: Is a command-line utility that allows users to run programs with the security privileges of another user, typically the root user

Depending on your hardware you may need additional drivers for your system to recognize and function correctly with the hardware, make sure to check your hardware specifications and check wether it requires additional drivers or firmware not included in `linux-firmware` for example; for hardware bug and security fixes for your cpu you may need to install a microcode updates firmware like `amd-ucode` or `intel-ucode`

Install the previously mentioned packages using the following command: 

```sh
$ pacstrap -K /mnt base linux linux-firmware networkmanager grub nano sudo
```
> **Note**: Only limited packages can be downloaded from the repository at this level, focus on a basic setup so your system can run after exiting and removing the install image and you can install more packages later

## CONFIGURE THE SYSTEM
To configure the system consist of manually constructing a minimal, yet functional, base system from which you can build upon. It ensures that all the necessary components are in place and correctly configured to allow the system to boot and run properly. By manually performing these steps, you gain a deep understanding of your system's inner workings and have the flexibility to tailor it to your specific needs. 

You will jump into your system directory and generate your user folder system, set timezone, system password and name, as well as the users that will login to the system, don't forget to setup network-manager before rebooting otherwise you will log in into a system without internet connection, unable to download packages.

This process is what distinguishes an Arch Linux installation and allows for the creation of a "vanilla" system, free from pre-installed bloatware or unnecessary services

🔸[**Follow this guide**](SYSTEM_CONFIG.md) **to setup configure the system**

## FINAL RECOMMENDATIONS: 

To make best use of your new system efficiently and safely I leave the following optional guides.

[**Connecting to Wi-Fi:**](CONNECT_WIFI_networkmanager.md)

[**Microcode:**](Microcode.md)

[**Power Management:**](Power_Management.md)

[**Firewall:**](Firewall.md)

[**Temperature Management:**](Temperature_Management.md)

Now all that is left is to install a desktop environment or window manager to create a more user friendly experience and enjoy your personalized and minimalist Arch computer.
