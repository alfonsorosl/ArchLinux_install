# ArchLinux_install
To install Arch follows instructions from the Installation guide from the ArchWiki, this guide will only dig deeper in specific steps to expand the understanding of the installation and to help learn more details about the system being installed

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
# sudo reflector --protocol https --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
```
The previous command selects the 10 most recently synchronized HTTPS mirrors, sorts them by download speed, and saves the result to the mirror list file.
If you want to limit the mirrors to specific countries, you can use the `--country` option:
```sh
# sudo reflector --country "Country1,Country2" --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
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

Install the previously mentioned packages using the following command: [INSTALL ONLY BASE LINUX AND LINUX FIRMWARE, touch later on other packages]

```sh
# pacstrap -K /mnt base linux linux-firmware networkmanager grub nano sudo
```

## CONFIGURE THE SYSTEM
- ### Generate Fstab
To get needed file systems (like the one used for the boot directory /boot) mounted on startup, generate an fstab (File System Table) file. This step mounts your file systems when your system boots
```sh
# genfstab -U /mnt >> /mnt/etc/fstab
```
and open the `fstab` to check all partitions are listed
```sh
# nano /mnt/etc/fstab
```
<details>
  <summary>If any of your partitions are missing</summary>

 It could be due to the order in which you mounted the partitions during the installation process. To resolve this, ensure that you mount the root partition first, then the other partitions. Visit the [Disk Partition Guide](DISK_PARTITION.md#mount-disk-partitions) to follow the steps to mount partitions again in right order and then redo the [Generate Fstab](README.md#generate-fstab) step
 </details>

- ### Chroot into the New System
To directly interact with the new system's environment, tools, and configurations for the next steps as if you were booted into it, change root into the new system
```sh
# arch-chroot /mnt
```
you can exit the environment type `exit` you can jump back and forth between the installation environment and your new system

- ### Set Time Zone
Set your time zone
```sh
# ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```
Then run `hwclock` to generate `/etc/adjtime`
```sh
# hwclock --systohc
```
This command assumes the hardware clock is set to UTC. Use the command `# timedatectl` to check
<details>
  <summary>If your hardware clock is not set to UTC follow this steps to change</summary>
You can see the list of timezones available with the following command:
 
 ```sh
# timedatectl list-timezones
```
Timezones are generally listed as `Continent/City` or `Country` or timezone abbreviations e.g: `GMT` you can filter the timezones you are looking for with the command:
```sh
# timedatectl list-timezones | grep abbreviation
``` 
to set the timezone to UTC use the command:
```sh
# timedatectl set-timezone UTC
```
  </p>
</details>

- ### Set Localization
These define the language and regional settings for the system. The locale is set in the `/etc/locale.conf` file, and it determines how applications display text, handle date and time formats, and manage other locale-specific standards

First edit the `/etc/locale.gen` with `nano`
```sh
# nano /etc/locale.gen
```
Inside the file uncomment the locales you need. You cannot actively use more than one locale at the same time, but you can generate multiple locales and select one as the active configuration <p>
Generally UTF-8 is a more complete character encoding since is a multibyte encoding that can represent any Unicode character

After uncommenting, generate your chosen locale by running
```sh
# locale-gen
```
And create the `/etc/locale.conf` file with your chosen locale LANG variable
```sh
# echo "LANG=en_US.UTF-8" > /etc/locale.conf
```
replace `en_US.UTF-8` with your chosen locale

Now set your console keyboard layout by running
```sh
# echo "KEYMAP=us" > /etc/vconsole.conf
```
replace `us` with your chosen keyboard layout, you can see the available layouts using `# localectl list-keymaps` and also filter the keymaps using the `grep` tool e.g. `# localectl list-keymaps | grep us`

- ### Set Hostname
This is a unique name that identifies your machine on a network. To assign a consistent, identifiable name to your system, create the hostname file
```sh
# echo "yourhostname" > /etc/hostname
```
(Replace yourhostname with your desired hostname)

Additionally, you should update the `/etc/hosts` file to reflect your hostname. This file maps hostnames to IP addresses
```sh
# nano /etc/hosts
```
Add the following lines (adjusting for your hostname)
```sh
127.0.0.1	  localhost
::1		       localhost
127.0.1.1	  yourhostname.localdomain	  yourhostname
```
To verify the host name you can run
```sh
# cat /etc/hostname
```

- ### Set Root Password
Linux systems require a password for the root account and, although it can be disabled, it is not recommended for security concerns. It is important to set the root password since Arch Linux does not set a root password by default during the installation process, and the installer does not automatically log you in.

To set the root password run
```sh
# passwd
```
and enter your desired root password twice

<Note> <If you forget to set a root password during installation, you can still recover by rebooting into the Arch ISO, remounting your partitions, and using arch-chroot to enter your install and set a password from there>

- ### Install Boot Loader (GRUB)
This step will show how to install GRUB as the system boot loader, if you choose a different boot loader refer to its instructions to install to the disk. This step is critical for your system to be bootable.
Identify your disk using `# lsblk -f` to confirm your disk name, it will not be necessary to identify partition number

And now install GRUB to the disk; if you are on a BIOS system use the following command:
```sh
# grub-install --target=i386-pc /dev/yourdiskname
```
Replace /dev/yourdiskname with your actual disk

For a UEFI system use the following command:
```sh
# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
```
You should have a separate EFI system partition mounted at `/boot/efi`, otherwise change the directory to the location where you have mounted your EFI partition

Next generate the GRUB configuration file
```sh
# grub-mkconfig -o /boot/grub/grub.cfg
```

- ### Create user and password

- ### Enable sudo

- ### Enable NetworkManager

- ### reboot-poweroff recommendations

recommendations: microcode   tlp   firewall   backup   auto-package-cache-cleaning   fstrim.timer-ssd-cleanup   thermald
