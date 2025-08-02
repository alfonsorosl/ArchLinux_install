# Configure the System

In this step you prepare the system to leave the install environment and remove the iso image, after this you will have your operating system working standalone with your hardware

- ### Generate Fstab
To get needed file systems (like the one used for the boot directory /boot) mounted on startup, generate an fstab (File System Table) file. This step mounts your file systems when your system boots
```sh
$ genfstab -U /mnt >> /mnt/etc/fstab
```
and open the `fstab` to check all partitions are listed
```sh
$ nano /mnt/etc/fstab
```
<details>
  <summary>If any of your partitions are missing</summary>

 It could be due to the order in which you mounted the partitions during the installation process. To resolve this, ensure that you mount the root partition first, then the other partitions. Visit the [Disk Partition Guide](DISK_PARTITION.md#mount-disk-partitions) to follow the steps to mount partitions again in right order and then redo the [Generate Fstab](README.md#generate-fstab) step
 </details>

- ### Chroot into the New System
To directly interact with the new system's environment, tools, and configurations for the next steps as if you were booted into it, change root into the new system
```sh
$ arch-chroot /mnt
```
you can exit the environment type `exit` you can jump back and forth between the installation environment and your new system

- ### Set Time Zone
Set your time zone
```sh
$ ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```
Then run `hwclock` to generate `/etc/adjtime`
```sh
$ hwclock --systohc
```
This command assumes the hardware clock is set to UTC. Use the command `# timedatectl` to check
<details>
  <summary>If your hardware clock is not set to UTC follow this steps to change</summary>
You can see the list of timezones available with the following command:
 
 ```sh
$ timedatectl list-timezones
```
Timezones are generally listed as `Continent/City` or `Country` or timezone abbreviations e.g: `GMT` you can filter the timezones you are looking for with the command:
```sh
$ timedatectl list-timezones | grep abbreviation
``` 
to set the timezone to UTC use the command:
```sh
$ timedatectl set-timezone UTC
```
  </p>
</details>

- ### Set Localization
These define the language and regional settings for the system. The locale is set in the `/etc/locale.conf` file, and it determines how applications display text, handle date and time formats, and manage other locale-specific standards

First edit the `/etc/locale.gen` with `nano`
```sh
$ nano /etc/locale.gen
```
Inside the file uncomment the locales you need. You cannot actively use more than one locale at the same time, but you can generate multiple locales and select one as the active configuration <p>
Generally UTF-8 is a more complete character encoding since is a multibyte encoding that can represent any Unicode character

After uncommenting, generate your chosen locale by running
```sh
$ locale-gen
```
And create the `/etc/locale.conf` file with your chosen locale LANG variable
```sh
$ echo "LANG=en_US.UTF-8" > /etc/locale.conf
```
replace `en_US.UTF-8` with your chosen locale

Now set your console keyboard layout by running
```sh
$ echo "KEYMAP=us" > /etc/vconsole.conf
```
replace `us` with your chosen keyboard layout, you can see the available layouts using `# localectl list-keymaps` and also filter the keymaps using the `grep` tool e.g. `# localectl list-keymaps | grep us`

- ### Set Hostname
This is a unique name that identifies your machine on a network. To assign a consistent, identifiable name to your system, create the hostname file
```sh
$ echo "yourhostname" > /etc/hostname
```
(Replace yourhostname with your desired hostname)

Additionally, you should update the `/etc/hosts` file to reflect your hostname. This file maps hostnames to IP addresses
```sh
$ nano /etc/hosts
```
Add the following lines (adjusting for your hostname)
```sh
127.0.0.1	  localhost
::1		       localhost
127.0.1.1	  yourhostname.localdomain	  yourhostname
```
To verify the host name you can run
```sh
$ cat /etc/hostname
```

- ### Set Root Password
Linux systems require a password for the root account and, although it can be disabled, it is not recommended for security concerns. It is important to set the root password since Arch Linux does not set a root password by default during the installation process, and the installer does not automatically log you in.

To set the root password run
```sh
$ passwd
```
and enter your desired root password twice

<Note> <If you forget to set a root password during installation, you can still recover by rebooting into the Arch ISO, remounting your partitions, and using arch-chroot to enter your install and set a password from there>

- ### Install Boot Loader (GRUB)
This step will show how to install GRUB as the system boot loader, if you choose a different boot loader refer to its instructions to install to the disk. This step is critical for your system to be bootable.
Identify your disk using `# lsblk -f` to confirm your disk name, it will not be necessary to identify partition number

And now install GRUB to the disk; if you are on a BIOS system use the following command:
```sh
$ grub-install --target=i386-pc /dev/yourdiskname
```
Replace /dev/yourdiskname with your actual disk

For a UEFI system use the following command:
```sh
$ grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
```
You should have a separate EFI system partition mounted at `/boot/efi`, otherwise change the directory to the location where you have mounted your EFI partition

Next generate the GRUB configuration file
```sh
$ grub-mkconfig -o /boot/grub/grub.cfg
```

- ### Create user and password

- ### Enable sudo

- ### Enable NetworkManager

- ### reboot
