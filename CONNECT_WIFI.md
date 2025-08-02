# CONNECT TO WIFI NETWORK (WITH IWD)
This page will guide you through the steps to connect to wifi using iwd

Enter iwctl interactive mode:
```sh
$ iwctl
```
You will see an `[iwd]$` prompt.

List your Wi-Fi devices:
```sh
[iwd]$ device list
```
This will show your wireless interface name (e.g., wlan0, wlp2s0). Note it down.

Power on your Wi-Fi device (if not already):
```sh
[iwd]$ device <your_device_name> set-property Powered on
```
Replace <your_device_name> with the name you found in the previous step.

Scan for available networks:
```sh
[iwd]$ station <your_device_name> scan
```
This command won't output anything, but it initiates the scan.

List available networks:
```sh
[iwd]$ station <your_device_name> get-networks
```
This will display a list of Wi-Fi networks, including their SSIDs (network names) and security types.

Connect to your desired network:
```sh
[iwd]$ station <your_device_name> connect "Your_SSID_Name"
```
   Replace `<your_device_name>` with your actual device name.

   Replace `"Your_SSID_Name"` with the exact name of your Wi-Fi network. Use quotes if the SSID contains spaces.

   If the network is secured, iwctl will prompt you for the password/passphrase. Enter it and press Enter.

Verify the connection:
```sh
[iwd]$ station <your_device_name> show
```
This will show the connection status. Look for "State: connected" and details about your connected network.

Exit iwctl:
```sh
[iwd]$ exit
```
Or press Ctrl+D.

Obtain an IP address (if not automatic):
By default, systemd-networkd (which iwd integrates with) often handles DHCP automatically. If you're not getting an IP address, you might need to manually start a DHCP client. In the Arch installation environment, dhcpcd is usually available:
Bash
```sh
$ dhcpcd <your_device_name>
```
Test the connection:
Bash
```sh
$ ping -c 3 archlinux.org
```
