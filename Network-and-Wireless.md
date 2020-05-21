# Networking and Wireless

## Overview

Options for networking:
- networkd
- netctl
- NetworkManager 


## Netctl

https://ricostacruz.com/til/arch-linux-wifi-using-netctl
http://www.linuxandubuntu.com/home/how-to-setup-a-wifi-in-arch-linux-using-terminal

### Summary

Summary:
- Create profile with wifi-menu
- Connect with netcl 
- Check for the status using iw dev


### Activate wifi

Use wifi-menu as root to generate the profile file in /etc/netctl/
```
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

### If having issues

Optional: You may have issues to start wireless profile, so this is a workaround.

Stop dhcpcd service
```
# systemctl stop dhcpcd.service 
# systemctl disable dhcpcd.service
# killall dhcpcd
```

Set link down
```
# ip link set wlan0 down
```

### Establish the connection

Once you have created your profile, attempt to establish a connection, where profile is only the profile name, not the full path:

```
# netctl start wlan0_home
```

Check that you're connected
```
# iw dev wlan0 link
```

Notes:
- Use netctl-auto instead of netctl. This gets you the benefits of "roaming" (auto-switching connections) and more.
- Removed the use of netctl enable <profile>. This works, but will make your boot time slower (Arch will wait for the connection to become successful before finishing the boot process).



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
```
# systemctl --type=service 
```

and then stop them. 

Install and start NetManager
```
# pacman -Syy networkmanager
# systemctl enable NetworkManager
```


### Check Wi-Fi Radio Status

Check if WiFi radio status. This can be done by executing the command below;
```
nmcli radio wifi
```

If the WiFi radio is disabled, then you can enable it by running the command below;
```
nmcli radio wifi on
```

You can as well check status of the network interface cards by running the command below;
```
nmcli dev status                 
 DEVICE  TYPE      STATE         CONNECTION 
 wlp2s0  wifi      disconnected  --         
 enp1s0  ethernet  unavailable   --         
 lo      loopback  unmanaged     -- 
```

### Check Available Wi-Fi Access Points
NetworkManager scans Wi-Fi networks periodically. To check if the SSID of Wi-Fi access point you want to connect to can be seen your Linux system, run the command below;

```
nmcli dev wifi list             
 SSID            MODE   CHAN  RATE       SIGNAL  BARS  SECURITY 
 Kmibey          Infra  2     54 Mbit/s  100     ▂▄▆█  WPA2     
 Ncheches house  Infra  1     54 Mbit/s  37      ▂▄_   WPA2 
```

 If you are unable to see the SSID of the Wi-Fi access point you want to connect to, you can run a rescan by executing the command below. After that, check again to if you can see the Access point SSID by re-executing the command above.

```
nmcli dev wifi rescan
```


### Connect to WiFi using NMCLI

Assuming that you already have the SSID and the connection password for the Access point you want to connect to, then execute the command below to connect.
```
sudo nmcli dev wifi connect Kmibey password 'mypassword'
 Device 'wlp2s0' successfully activated with 'a1900bed-baa9-47a3-affb-b640d0effe5d'.
```

If you do not want to display password in plain text, then you can pass the –ask option as shown below.
```
sudo nmcli --ask dev wifi connect Kmibey
 Password: 
 Device 'wlp2s0' successfully activated with 'f747251b-1346-48a2-ae25-1b6fd6243984'.
```


### Check Device status

```
nmcli dev status                                        
 DEVICE  TYPE      STATE        CONNECTION 
 wlp2s0  wifi      connected    Kmibey     
 enp1s0  ethernet  unavailable  --         
 lo      loopback  unmanaged    -- 
```

### Check Active Connections

Once you have successfully connected to your AP, then you can check active connections using the command below;
```
nmcli con show --active                                
 NAME    UUID                                  TYPE             DEVICE 
 Kmibey  a1900bed-baa9-47a3-affb-b640d0effe5d  802-11-wireless  wlp2s0
```

You can as well omit the –active option to list all the connections and their status.


### Delete Established Connections

If you need to delete the connection that is already established, you can specify the connection UUID or the connection name. To obtain the connection UUID or the username, run the nmcli con show command as shown below;

```
nmcli con sh
 NAME                UUID                                  TYPE             DEVICE 
 Kmibey              41dc9830-dd20-4deb-92be-371bfb5d16f0  802-11-wireless  wlp2s0 
 Beats               949762b0-c6e0-4004-918a-55fb6bcf6610  802-11-wireless  --
```

To delete using a connection using connection name;
```
sudo nmcli con del Kmibey
 Connection 'Kmibey' (41dc9830-dd20-4deb-92be-371bfb5d16f0) successfully deleted.
```

To delete a connection using connection UUID;
```
sudo nmcli con del 41dc9830-dd20-4deb-92be-371bfb5d16f0
 Connection 'Kmibey' (41dc9830-dd20-4deb-92be-371bfb5d16f0) successfully deleted.
```

You can also take down or bring up a previous connection using the commands below respectively.
```
nmcli con down <SSID or UUID>
nmcli con up <SSID or UUID>
```




## Networkd

ArchWiki: https://wiki.archlinux.org/index.php/systemd-networkd#Wireless_adapter

In order to connect to a wireless network with systemd-networkd, a wireless adapter configured with another application such as WPA supplicant or Iwd is required.

The systemd package is part of the default Arch installation and contains all needed files to operate a wired network. Wireless adapters can be setup by other services, such as wpa_supplicant

