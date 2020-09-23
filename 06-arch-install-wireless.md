
# Wireless

Summary:
- Connect to network using netctl (default in ArchLinux ISO)
- Once connectivity is established, install NetworkManager
- Connect to network with NetworkManager

<br>

## Option1 - Connect to wireless with nettcl

Up to ArchLinux 20.3 (July 2020), Use wifi-menu to generate the profile file in /etc/netctl/.

To get the name of your wireless interface do:

```bash
# iw dev
```

For wireless settings, use wifi-menu to generate the profile file in /etc/netctl/. Make sure wifi interface is down before using wifi-menu.
```bash
# wifi-menu
```

Check you are successfully associated to your wifi:
```bash
# iw dev
# iw dev wlan0 link
```

At this point you have access to the network - Check by typing:
```bash
# ping 8.8.8.8
# ping archlinux.org
```

<br>

## Option 2 - Connect to wireless using iwd

From 20.3 and above, **wifi-menu** and **netctl** are not included anymore in the iso, there is a new tool called **iwd**.

Get the wifi interface name

```
# iwctl
[iwd]# device list
                                  Devices
----------------------------------------------------------------------------
Name                 Address            Powered   Adapter    Mode
----------------------------------------------------------------------------
wlan0                4c:32:75:93:32:b5  on        phy0       station
[iwd]#
```

Scan wifi interface

```
[iwd]# station wlan0 scan
```

Display SSIDs

```
[iwd]# station wlan0 get-networks
                     Available Networks
-------------------------------------------------------
  Network name                 Security   Signal
-------------------------------------------------------
  > Livebox-XXXX                psk       ****

[iwd]#
```

Then connect to your SSID

```
[iwd]# station wlan0 connect Livebox-XXXX
[iwd]# exit
```

Check that you have an IP address:

```bash
# ip addr
```

At this point you have access to the network - Check by typing:

```bash
# ping 8.8.8.8
# ping archlinux.org
```

<br>

## Update package database and install NetworkManager

Install and start NetManager
```bash
# pacman -Syy networkmanager
# systemctl enable NetworkManager
```

You must ensure that no other service that wants to configure the network is running; in fact, multiple networking services will conflict. You can find a list of the currently running services with:
```bash
# systemctl --type=service 
# systemctl list-unit-files --state=enabled
```

Stop netctl and wpa_supplicant
```bash
# systemctl stop netctl@wlan0_home.service
# systemctl stop wpa_supplicant.service
```

<br>

## Migrate to NetworkManager

Check if WiFi radio status. This can be done by executing the command below;
```bash
# nmcli radio wifi
```

If the WiFi radio is disabled, then you can enable it by running the command below;
```bash
# nmcli radio wifi on
```

Check status of network interfaces
```bash
# nmcli dev status                 
```

Display the list of SSID
```bash
# nmcli dev wifi list
```

 If SSID not listed, rescan
```bash
# nmcli dev wifi rescan
```

Connect to WiFi
```bash
# nmcli dev wifi connect Livebox-B00A password 'mypassword'
```

If you do not want to display password in plain text, then you can pass the –ask option as shown below.
```bash
# nmcli --ask dev wifi connect Livebox-B00A
```

Check Device status
```bash
# nmcli dev status
```

Check Connections (--active to only list ones)
```bash
# nmcli con show                               
 NAME    UUID                                  TYPE             DEVICE 
 Kmibey  a1900bed-baa9-47a3-affb-b640d0effe5d  802-11-wireless  wlp2s0
```

<br>

## Troubleshooting

If wifi device is unavailable, Check NetworkManager status:
```bash
# systemctl status NetworkManager.service'
```

And see what is the conflict: wpa_supplicant could be one (if yes then stop).
```bash
# systemctl stop wpa_supplicant.service
```
