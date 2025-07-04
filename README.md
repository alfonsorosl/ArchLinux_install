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

## INSTALL
Welcome to the ArchLinux installation console, select the install medium and you will be presented with a virtual console as the root user with a zsh shell prompt. The console will have by default the US keymap and the packages listed in this file https://geo.mirror.pkgbuild.com/iso/latest/arch/pkglist.x86_64.txt will be available. *To change the keymap to match your keyboard or change to different console, refer to the ArchWiki*

You will setup Arch now:
- ### Connect to internet
 First step is to connect to the internet
    Ethernet > plug the cable and check connection with ping
    Wifi > follow instructions to authenticate wireless network with iwctl https://wiki.archlinux.org/title/Iwctl

Now you are connected to the internet your system should synchronize its clock, verify that it is correct entering into the console:
```sh
# timedatectl
```
