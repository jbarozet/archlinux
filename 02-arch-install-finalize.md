# Arch Installation Part2 (Finalize)

Once restarted, login as root.
NetworkManager is installed (or wicd), so networking should work fine.

Disable wpa_supplicant which will conflict with NetworkManager
```bash
# systemctl stop wpa_supplicant
# systemctl disable wpa_supplicant
```

## Wired connection

You should be good to go. Check that you have an IP Address with:
```bash
# ip addr
```


## Wireless connection

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

Check Device status
```bash
# nmcli dev status
```

Check Connections (--active to only list ones)
```bash
# nmcli con show                               
 NAME    UUID                                  TYPE             DEVICE 
 Livebox a1900bed-baa9-47a3-affb-b640d0effe5d  802-11-wireless  wlan0
```


## Add NTP and Cron

Adding NTP (Clock sync) and cronie (admin task automation)

```
pacman -Syy ntp cronie
```

If we want log in clear, then update file /etc/systemd/journald.conf and proceed with the following modifications. Replace:

```
#ForwardToSyslog=no
```
with:
```
ForwardToSyslog=yes
```


## Video

Install drivers for video cards. xf86-video-vesa package includes a lot of drivers.
Check this for more information: https://wiki.archlinux.org/index.php/Xorg#Driver_installation

Nvidia 
- check wiki ttps://wiki.archlinux.org/index.php/NVIDIA. 
- ptimus : https://wiki.archlinux.org/index.php/NVIDIA_Optimus

VirtualBox
- In addition to xf86-video-vesa, install virtualbox-guest-utils

```
pacman -S xf86-video-vesa
pacman -S virtualbox-guest-utils
```

Enable kernel modules:
```
systemctl enable vboxservice
```


## Fonts

```
pacman -S ttf-{bitstream-vera,liberation,freefont,dejavu} freetype2
```

Luke Smith:
- ttl-linux-libertine
- console: ttf-inconsolata
- 

## Create User

Create a user and add to wheel group. Add password.

```
# useradd -m -g wheel -c 'Jean-Marc Barozet' -s /bin/bash jmb 
# passwd jmb
```


## Modify SUDOERS

To give access to sudo command, change the sudoers using command visudo. Do not update directly /etc/sudoers.
```
EDITOR=vim visudo
```


## AUR

Installer Trizen ou Yay pour compléter Pacman.
On peut utiliser trizen (écrit en perl) ou Yay (écrit en Go) à la place du vieillissant yaourt.

Install Trizen :

```
sudo pacman -S git
git clone https://aur.archlinux.org/trizen
cd trizen
makepkg -sri
```

Or install Yay :

```
sudo pacman -S git
git clone https://aur.archlinux.org/yay
cd yay
makepkg -sri
```


## pamac

Now to install Pamac:

#install Pamac
$ yay -S pamac-aur

It will ask for some inputs.  I selected 1.) pamac-aur
I removed dependencies after install
I selected None on the next prompt.



Note:
	• Yaourt and Packer => AUR helpers
	• Replaced par Yay - Yet Another Yaourt




## Enable Services

systemd being used, here is a list of services that may have to be activated:
- syslog
- cron
- avahi, avahi-dnsconfd: CUPS
- org.cups.cupsd: printers
- bluetooth
- NTP to synchronize clock from the network

```
systemctl enable syslog-ng@default
systemctl enable cronie
systemctl enable avahi-daemon
systemctl enable avahi-dnsconfd
systemctl enable org.cups.cupsd
systemctl enable bluetooth
systemctl enable ntpd
```


Check bluetooth and syslog

