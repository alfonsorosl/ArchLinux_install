# Connect to Wifi using NetworkManager

If you have installed NetworkManager
```sh
sudo pacman -S networkmanager
```
You can use nmtui (text-based) or nmcli (command-line):

Using nmtui: type in terminal `nmtui` then select “Activate a connection” and follow UI steps.

Using nmcli:
```sh
$ nmcli device wifi rescan        # Rescan for Wi-Fi networks
$ nmcli device wifi list          # List available Wi-Fi networks
$ nmcli device wifi connect "Your_SSID" password "Your_Wi-Fi_Password"
```
