# Configure a Firewall
**UFW** provides a user-friendly command-line interface for managing iptables, simplifying the process of setting up network filtering rules. To install type:

```bash
$ sudo pacman -S ufw
```

Once installed, it is advised to configure UFW with a default policy that denies incoming traffic and allows outgoing traffic, which provides a secure baseline.
```bash
$ ufw default deny incoming
$ ufw default allow outgoing
```
