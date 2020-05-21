# Networking and Wireless

## Overview

Options for networking:
- networkd
- netctl
- NetworkManager 

<br>

## Netctl

https://ricostacruz.com/til/arch-linux-wifi-using-netctl
http://www.linuxandubuntu.com/home/how-to-setup-a-wifi-in-arch-linux-using-terminal

### Summary

Summary:
- Create profile with wifi-menu
- Connect with netcl 
- Check for the status using iw dev

To get the name of your wireless interface do:
```bash
# iw dev
```

Use wifi-menu as root to generate the profile file in /etc/netctl/
```bash
# wifi-menu
```

=> wlan0_home profile generated in /etc/netctl

```
Description='Automatically generated profile by wifi-menu'
Interface=wlan0
Connection=wireless
Security=wpa
ESSID=Livebox-B00A
IP=dhcp
Key=xxxxxxxxxxxxxxxx
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

Once you have created your profile, attempt to establish a connection, where profile is only the profile name, not the full path:

```bash
# netctl start wlan0_home
```

Check that you're connected
```bash
# iw dev wlan0 link
```

Notes:
- Use netctl-auto instead of netctl. This gets you the benefits of "roaming" (auto-switching connections) and more.
- Removed the use of netctl enable <profile>. This works, but will make your boot time slower (Arch will wait for the connection to become successful before finishing the boot process).


<br>

## NetworkManager

NetworkManager: https://wiki.archlinux.org/index.php/NetworkManager
Connect to WiFi using nmcli: https://kifarunix.com/connect-to-wifi-in-linux-using-nmcli-command/
nmtui: https://jlk.fjfi.cvut.cz/arch/manpages/man/nmtui.1

NetworkManager can be installed with the package networkmanager, which contains a daemon, a command line interface (nmcli) and a curses‐based interface (nmtui).

For the most part, I find NetworkManager to be a preferable choice over netctl for desktop systems. It works, it integrates with GNOME and Plasma, it has a lot of user interfaces (nmtui being my favorite—a CLI version!), has support for OpenVPN, and many more.

NetworkManager sits in the background and makes connections to known networks.  You need a tool to twist its knobs.  You need to be able to see the results of a site survey.  You need to be able to provide credentials. You need to be able to disconnect an access point, or force connection to another. 
- There are command line tools (nmcli, nmtui).  There are GUI front ends (nm-applet, Gnome network-manager-applet, KDE plasma-nm.  There are front ends for dmenu (nmcli-dmenu)
- There are tools that are*NOT* for controlling NetworkManager -- Notably, wifi-menu


### Check for any conflict

You must ensure that no other service that wants to configure the network is running; in fact, multiple networking services will conflict. You can find a list of the currently running services with:
```bash
# systemctl --type=service 
# systemctl list-unit-files --state=enabled
```

Stop netctl and wpa_supplicant (if needed)
```bash
# systemctl stop netctl@wlan0_home.service
# systemctl stop wpa_supplicant.service
```

Install and start NetManager
```bash
# pacman -Syy networkmanager
# systemctl enable NetworkManager
```

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



### Delete Established Connections

If you need to delete the connection that is already established, you can specify the connection UUID or the connection name. To obtain the connection UUID or the username, run the nmcli con show command as shown below;

```bash
# nmcli con sh
 NAME                UUID                                  TYPE             DEVICE 
 Kmibey              41dc9830-dd20-4deb-92be-371bfb5d16f0  802-11-wireless  wlp2s0 
 Beats               949762b0-c6e0-4004-918a-55fb6bcf6610  802-11-wireless  --
```

To delete using a connection using connection name;
```bash
# sudo nmcli con del Kmibey
 Connection 'Kmibey' (41dc9830-dd20-4deb-92be-371bfb5d16f0) successfully deleted.
```

To delete a connection using connection UUID;
```bash
# sudo nmcli con del 41dc9830-dd20-4deb-92be-371bfb5d16f0
 Connection 'Kmibey' (41dc9830-dd20-4deb-92be-371bfb5d16f0) successfully deleted.
```

You can also take down or bring up a previous connection using the commands below respectively.
```bash
# nmcli con down <SSID or UUID>
# nmcli con up <SSID or UUID>
```


<br>


## Networkd

ArchWiki: https://wiki.archlinux.org/index.php/systemd-networkd#Wireless_adapter

In order to connect to a wireless network with systemd-networkd, a wireless adapter configured with another application such as WPA supplicant or Iwd is required.

The systemd package is part of the default Arch installation and contains all needed files to operate a wired network. Wireless adapters can be setup by other services, such as wpa_supplicant



## Troubleshooting

If having issues

Optional: You may have issues to start wireless profile, so this is a workaround.

Stop dhcpcd service
```bash
# systemctl stop dhcpcd.service 
# systemctl disable dhcpcd.service
# killall dhcpcd
```

Set link down
```bash
# ip link set wlan0 down
```