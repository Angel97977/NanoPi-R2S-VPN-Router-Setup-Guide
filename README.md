## NanoPi R2S VPN Router Setup Guide

This guide provides step-by-step instructions to set up a NanoPi R2S as a secure VPN router with FriendlyWrt. It includes all hardware requirements, software configurations, and optional security measures to ensure a functional and secure setup.

_Table of Contents_

_Hardware Requirements_

_Initial Setup_

_Network Configuration_

_WiFi Configuration_

_Security Measures_

_Testing and Troubleshooting_

_Future Improvements_

***

### Hardware Requirements

Ensure you have the following items:

* NanoPi R2S

* MicroSD card (16GB or higher)

* Pre-flash with the FriendlyWrt firmware. You need to flash the micro SD cards with the help of the oficial images. 
    Visit [download here](https://download.friendlyelec.com/NanoPiR2S) to download image files (in the "01_Official images/01_SD card images" directory);
    Download the win32diskimager tool (in the "05_Tools" directory), or use your preferred tool;
    Extract the .gz format compressed file to get the .img format image file;
    Run the win32diskimager utility under Windows as administrator. On the utility's main window select your SD card's drive, the wanted image file and click on "write" to start flashing the SD card.
    Take out the SD and insert it to NanoPi-R2S's microSD card slot;
    Power on NanoPi-R2S and it will be booted from your TF card, some models may require pressing the Power button to start; 

* 5V/3A power adapter

* Ethernet cables (at least 2):

* One for connecting to your router (WAN).

* One for connecting to your computer (LAN) during initial setup.

* Router with internet access

***

### Initial Setup

1.Flash FriendlyWrt

 *  Download the FriendlyWrt firmware from the official site.

2.Connect the NanoPi R2S

  *  Insert the MicroSD card into the NanoPi R2S.

  *  Connect the LAN port to your computer using an Ethernet cable.

  *  Power up the NanoPi R2S using the 5V/3A adapter.

3.Access the NanoPi

  *  Set your computer’s IP address to a static IP in the 192.168.1.x range.

  *  Access the NanoPi via SSH whether it's a tool like putty or the one of your preference. The ip direction can be modify on LuCi, the explanation is in Network Configuration (below).

  *  The default password is password. Change it immediately:
To change the password the command input is `password`. 

## Notice: The first time you need to connect the NanoPi directly to your router (port WAN), You need to wait for a while (about 2~3 minutes) to boot up for the first time, and then set FriendlyWrt, you can enter the ttyd terminal on the openwrt webpage, when the prompt is displayed as root@FriendlyWrt, it means the system has been initialize connect directly to your computer via LAN. 

How do you know if you have access inside the NanoPi?
If you're inside the Nano you were seeing something like this.
![image](https://github.com/user-attachments/assets/559c275a-19c5-45fc-bfdb-10f1599d3208)

With the message 'Friendlywrt' in it. At this point, you can begin with the software setups on the firmware, dropbear and else.

### Network Configuration
1) Click on Network → Interfaces, then click on the Edit button of the LAN Network;
2) In General Setup tab, input new IP address (for example: 192.168.11.1), click "Save" and then click "Save & Apply";
3) On the pop-up window with the title “Connectivity change“, click "Apply and revert on connectivity loss";
4) Wait a moment, enter the new address in your computer's browser and login to FriendlyWrt;

## Notice: Always remember to poweroff the device inside the terminal with the `poweroff`command and hit enter, wait until the led light is off, and then unplug the power supply.

In this case I use an Access Point, or better known as AP. Because I'm wasn't able to connect to wifi between my computer directly connected to LAN port on the NanoPi RS2. I use the following table to get to know much better the types of AP's and the preinstalled driver's on each one.
![image](https://github.com/user-attachments/assets/b4703065-d71d-43a4-9d3f-f2d93afbe603)

### WiFi Configuration

1. Configure WiFi Access Point (In my case, I use the rtl8812au AP). And I really got no issues on the way. But I do a configuration inside the NanoPi, because it hasn't provide the power suply on the USB port. 
Check the power suply on the USB port
Execute the following command to ensure the energy doesn't be in sleep mode 

`cat /sys/bus/usb/devices/usb3/power/control`

If returns auto, switch it to on to force the power suply on that USB port. 

`echo on > /sys/bus/usb/devices/usb3/power/control`

Force driver module reconfiguration
Remove and reload the driver module to ensure it initializes correctly:

rmmod rtl8812au
modprobe rtl8812au
Then check again to see if the Wi-Fi adapter appears: 

`lsusb`

Configure the Wi-Fi adapter manually
Try to enable the Wi-Fi interface created by the adapter:

List the available network interfaces:

`ifconfig -a`

Look for an interface similar to wlan0 or wlx.... If you find it, enable it:

`ifconfig wlan0 up`

Check if the adapter is now active:

`iw dev`

If the led on the AP goes on. Then you must modify the following files:
Go to `vi /etc/config/wireless` to set up your WiFi: (Explained on the code part)
Security Measures

1. Restrict SSH Access

Restrict SSH access to the LAN interface:

Edit the dropbear configuration:
`vi /etc/config/dropbear`
Set the following option:
option Interface 'lan'
Restart the SSH service with:
`/etc/init.d/dropbear restart`
At this point, you need to plug directy to the router on WAN port. Then you can do the following steps

### Testing and Troubleshooting

1. Verify Connectivity

Test internet access:

 `ping 8.8.8.8`

Check LAN and WAN interfaces:

  `ifconfig`

2. Debugging

View system logs for errors:

 ` 'logread'`

Check service statuses:

  `'/etc/init.d/network status'`

### Future Improvements

Install VPN Set up a VPN service (e.g., WireGuard or OpenVPN) for secure remote access.

Monitor Traffic Use tools like luci-app-statistics to visualize network traffic.

Firewall Configuration Harden your firewall rules to block unnecessary traffic.


I was testing the following link, some things works but in some cases not. I only put it as reference on future works. And of course it's adapted to the NanoPi RS2 [Reference Nano Setup](https://wiki.friendlyelec.com/wiki/index.php/NanoPi_R2S)

***

By following this guide, you’ll have a functional and secure NanoPi R2S acting as a WiFi router with VPN capabilities. For questions or contributions, feel free to open an issue or pull request in this repository!


