# Arch Installation Part2 (Finalize)

Once restarted, login as root.
NetworkManager is installed (or wicd), so networking should work fine.

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

Il faut ensuite choisir le pilote pour le circuit vidéo. Voici les principaux pilotes, sachant que le paquet xf86-video-vesa englobe une énorme partie des circuits graphiques, dont ceux non listés dans le tableau un peu plus loin. En cas de doute : https://wiki.archlinux.org/index.php/Xorg#Driver_installation

Pour Nvidia, c’est un casse-tête au niveau des pilotes propriétaires. Le plus simple est de se référer au wiki d'Archlinux : https://wiki.archlinux.org/index.php/NVIDIA. Et si vous avez la technologie Optimus : https://wiki.archlinux.org/index.php/NVIDIA_Optimus

Si vous faites une installation dans VirtualBox, il faut deux paquets. En plus de xf86-video-vesa, il faut le paquet virtualbox-guest-utils. Cependant, il y a deux choix qui arrivent pour ce paquet.

Ce qui donne :
```
pacman -S xf86-video-vesa
pacman -S virtualbox-guest-utils
```

La prise en charge des modules noyau se fait avec la commande systemctl suivante :
```
systemctl enable vboxservice
```


## Fonts

```
pacman -S ttf-{bitstream-vera,liberation,freefont,dejavu} freetype2
```

Luke Smith:
- ttl-linux-libertine


## Create User

On crée un utilisateur avec la commande suivante, qui sera indispensable pour appliquer un des addenda si vous ne voulez pas utiliser Gnome.

```
# useradd -m -g wheel -c 'Jean-Marc Barozet' -s /bin/bash jmb 
# passwd jmb
```


## Modify SUDOERS

Pour que l’on puisse accéder en tant qu’utilisateur classique aux droits complets sur la machine de manière temporaire.
Edit /etc/sudoers using visudo command.
```
EDITOR=vim visudo
```



## Enable Services

systemd being used, here is a list of services that may have to be activated.

```
systemctl enable syslog-ng@default → *gestion des fichiers d’enregistrement d’activité*
systemctl enable cronie → *pour les tâches récurrentes*
systemctl enable avahi-daemon → *dépendance de Cups*
systemctl enable avahi-dnsconfd → *autre dépendance de Cups*
systemctl enable org.cups.cupsd → *cups pour les imprimantes*
systemctl enable bluetooth → *uniquement si on a du matériel bluetooth*
systemctl enable ntpd → *pour synchroniser l’heure en réseau.*
```


