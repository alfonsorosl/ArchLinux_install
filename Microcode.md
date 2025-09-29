# Install Microcode Updates
Critical for system stability, security, and performance, as they fix hardware bugs, vulnerabilities (such as Meltdown and Spectre), and other issues that can lead to crashes or unexpected system halts from Intel or AMD processors.

For Intel processors type:

```bash
$ sudo pacman -S intel-ucode
```

For AMD processors type:

```bash
$ sudo pacman -S amd-ucode
```

**IMPORTANT** - For the updates to load after boot run after install:

```bash
sudo mkinitcpio -P
```
