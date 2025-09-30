# Power Management
**TLP** is a tool that automatically adjusts various hardware settings to improve energy efficiency without requiring deep technical knowledge. For most users, the default TLP settings are already well-optimized for battery performance, making it a "set and forget" solution.
```bash
$ sudo pacman -S tlp
```

And enable service:
```bash
$ sudo systemctl enable tlp
$ sudo systemctl start tlp
```

Advanced users can fine-tune its behavior by editing the configuration file located at `/etc/tlp.conf`
