# Configure a Firewall
**UFW** provides a user-friendly command-line interface for managing iptables, simplifying the process of setting up network filtering rules. To install type:

```bash
$ sudo pacman -S ufw
```
And enable service:
```bash
$ sudo systemctl enable ufw
$ sudo systemctl start ufw
```

Once installed, it is advised to configure UFW with a default policy that denies incoming traffic and allows outgoing traffic, which provides a secure baseline.
```bash
$ sudo ufw default deny incoming
$ sudo ufw default allow outgoing
```
