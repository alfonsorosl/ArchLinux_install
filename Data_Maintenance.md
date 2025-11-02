# Data Backup and Cleanup
This guide provides steps to set up critical maintenance tasks to backup and clean your system, ensuring data safety and better system performance.

### BACKUP DATA
Regular backups are essential for recovering from data loss or system failure. We will use the rsync utility for efficient incremental backups of user data.

**Install rsync**
```bash
$ sudo pacman -S rsync
```
> **rsync** (short for Remote Sync) is a command-line utility for copying and synchronizing files and directories, locally and across networked systems. Its key strength is its delta-transfer algorithm, which makes it incredibly efficient, especially for backups.
Instead of blindly copying all files from the source to the destination every time, rsync first checks both locations. For any file that exists in both places, it calculates a checksum.
The first time you run rsync, it copies all files and directories completely, just like a standard cp command.
On every run after the first, rsync only transfers:
- New files.
- Files that have changed in size or modification time.
- Crucially, for files that have changed, it only transfers the changed blocks of data (the "delta") within that file, not the entire file.

**Perform User Data Backup**
This command backs up your entire /home/yourusername/ directory (where yourusername is your actual username) to an external drive or network share mounted at /mnt/backup_drive.
|Command |Description |
|--------|------------|
|-a	|Archive mode: Recursively copy files and preserve symbolic links, permissions, modification times, group, and owner.|
|-v	|Verbose: Shows files being transferred.|
|--progress	|Displays progress during file transfer.|
|--exclude	|Skip specified files or directories (e.g., large cache folders).|

```bash
$ rsync -av --progress --exclude='Music' --exclude='Videos' /home/yourusername/ /mnt/backup_drive/home_backup/
```
> NOTE: You should exclude large, non-essential directories like media or large application caches to speed up the process and save space. The trailing slash (/) on the source (/home/yourusername/) is critical; without it, rsync copies the directory itself into the destination.

### AUTOMATIC PACKAGE CACHE CLEANING (paccache)
The Arch Linux package manager, pacman, keeps all downloaded packages in a local cache (/var/cache/pacman/pkg/). This allows downgrading or reinstalling packages without an internet connection but can consume significant disk space over time. The recommended utility for cleanup is paccache, which is part of the pacman-contrib package.

**Install pacman-contrib**
```Bash
$ sudo pacman -S pacman-contrib
```

> **pacman-contrib** enhances the functionality of Pacman. It manages the size of the Pacman package cache (/var/cache/pacman/pkg/) allowing for automation and for saving disk space while still retaining enough older packages to safely downgrade a package.
It includes example Pacman hook scripts (located in /usr/share/doc/pacman-contrib/examples/). These are scripts that automatically run before or after a Pacman transaction.
Also includes `rankmirrors`, a script that helps sort your Pacman mirror list based on connection speed. And `checkupdates`, a script that safely checks for new package updates without running a full database sync.

**Setup Automated Weekly Cleanup with `paccache.timer`**
The pacman-contrib package provides a systemd timer unit for automated weekly cleaning. By default, it runs paccache -r which keeps the three most recent versions of every installed and uninstalled package.
|Command	|Description |
|---------|------------|
|paccache -r	|Cleans the package cache, keeping the three most recent versions of installed and uninstalled packages.|
|paccache -rk2	|Cleans the cache, keeping the two most recent versions (-k2) of installed packages (-r).|

To enable the weekly timer:
```Bash
$ sudo systemctl enable paccache.timer
$ sudo systemctl start paccache.timer
```

**Verify the Timer Status**
Check the timer's status and when it is scheduled to run next.
```Bash
$ systemctl status paccache.timer
$ systemctl list-timers | grep paccache
```

### SSD MAINTENANCE (fstrim.timer)
For Solid State Drives (SSDs), the TRIM command is necessary for maintaining performance and longevity. TRIM tells the SSD which data blocks are no longer in use and can be safely erased. On Arch Linux, this is handled periodically by a systemd timer.
> WARNING: This section is relevant only if you are using an SSD or NVMe drive. Do not use fstrim on traditional Hard Disk Drives (HDDs).

**Setup Automatic Weekly TRIM**
The util-linux package, which is part of the base group, already provides the fstrim.timer unit. This timer runs the fstrim.service weekly.

Enable and start the weekly timer:
```Bash
$ sudo systemctl enable fstrim.timer
$ sudo systemctl start fstrim.timer
```

**Verify the Timer Status**
Check the status to ensure it is active and scheduled.
```Bash
$ systemctl status fstrim.timer
$ systemctl list-timers | grep fstrim
```

**Manually Run TRIM (Optional)**
You can manually execute a TRIM operation at any time to discard unused blocks on all mounted filesystems that support the operation.
Command	Description
-v	Verbose: Shows the amount of space trimmed.
```Bash
$ sudo fstrim -Av
```
