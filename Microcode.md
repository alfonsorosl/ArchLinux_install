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
<details>
  <summary>**IMPORTANT** - For the updates to load after boot run after install:</summary>
<p>regenerates the initramfs (initial ramdisk) image, which is essential for the early boot process. The initramfs must include the microcode update files so they can be loaded early during boot, before the main system filesystem is mounted. This ensures that the CPU's microcode is updated as early as possible, which is critical for system stability and security.
  Without this step, the microcode updates provided by the intel-ucode package will not be included in the initramfs, potentially leading to system instability or crashes</p>
</details>

```bash
sudo mkinitcpio -P
```

> **NOTE** `mkinitcpio -P` regenerates the initramfs image, which is where the microcode update is embedded. The bootloader's role is to load this initramfs early in the boot process. As long as the bootloader is configured to load the initramfs (which is standard), the microcode update will be applied.
