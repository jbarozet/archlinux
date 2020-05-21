
# Wireless


## Reference

More information:
- https://wiki.archlinux.org/index.php/Network_configuration/Wireless
- https://wiki.archlinux.org/index.php/Wpa_supplicant
- https://wiki.archlinux.org/index.php/Netctl
- https://zestedesavoir.com/billets/3113/archlinux-et-installation-en-wifi/
- https://unix.stackexchange.com/questions/121060/arch-linux-not-connecting-to-wifi-anymore/121125#121125



## Using netctl (default in ArchLinux ISO)

Check the output of the ip link command to see if a wireless interface was created; usually the naming of the wireless network interfaces starts with the letter "w", e.g. wlan0 or wlp2s0. Then bring the interface up with:
```
# ip link set interface up
```

To get the name of your wireless interface do:
```
$ iw dev
```

To check link status, use following command.

```
$ iw dev interface link
```

You can get statistic information, such as the amount of tx/rx bytes, signal strength etc., with following command:
```
$ iw dev interface station dump
```

Discover access points
To see what access points are available:

```
# iw dev interface scan | less
```

The important points to check:
- SSID: the name of the network.
- Signal: is reported in a wireless power ratio in dBm (e.g. from -100 to 0). The closer the negative value gets to zero, the better the signal. Observing the reported power on a good quality link and a bad one should give an idea about the individual range.
- Security: it is not reported directly, check the line starting with capability. If there is Privacy, for example capability: ESS Privacy ShortSlotTime (0x0411), then the network is protected somehow.
    - If you see an RSN information block, then the network is protected by Robust Security Network protocol, also known as WPA2.
	- If you see an WPA information block, then the network is protected by Wi-Fi Protected Access protocol.
	- In the RSN and WPA blocks you may find the following information:
			* Group cipher: value in TKIP, CCMP, both, others.
			* Pairwise ciphers: value in TKIP, CCMP, both, others. Not necessarily the same value than Group cipher.
			* Authentication suites: value in PSK, 802.1x, others. For home router, you will usually find PSK (i.e. passphrase). In universities, you are more likely to find 802.1x suite which requires login and password. Then you will need to know which key management is in use (e.g. EAP), and what encapsulation it uses (e.g. PEAP). See #WPA2 Enterprise and Wikipedia:Authentication protocol for details.
	- If you see neither RSN nor WPA blocks but there is Privacy, then WEP is used.


Regardless of the method used, you can check if you have associated successfully:

```
# iw dev
```

For wireless settings, you can use wifi-menu as root to generate the profile file in /etc/netctl/
```
# wifi-menu
```

Check you are successfully associated to your wifi:
```
# iw dev interface link
```

At this point you have access to the network - Check by typing:
```
# ping 8.8.8.8
```

Once you have created your profile, attempt to establish a connection, where profile is only the profile name, not the full path:

```
# netctl start wlan0_home
```

Check that you're connected
```
# iw dev
# iw dev wlan0 link
```


## NetworkManager





