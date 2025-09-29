# Temperature Management
Prevent overheating by monitoring temperature and applying cooling measures through available methods, such as adjusting fan speeds or throttling CPU performance.
For Intel CPUs `thermald` can be used and for AMD CPUs `amdctl` can be used

**Intel:**
```bash
$ sudo pacman -S intel-ucode
```
After installation, it has to be enabled and started to run automatically at boot:
```bash
$ sudo systemctl enable thermald
$ sudo systemctl start thermald
```
For optimal performance, a configuration file `/etc/thermald/thermal-conf.xml`

**AMD:**
To install `amdctl` you will have to use the AUR https://aur.archlinux.org/packages/amdctl to install AUR packages [**Follow this guide**](USE_AUR_Repositories.md).

After installation, it has to be enabled and started to run automatically at boot:
```bash
$ sudo systemctl enable amdctl
$ sudo systemctl start amdctl
```
For optimal performance, a configuration file `/etc/amdctl.conf`
