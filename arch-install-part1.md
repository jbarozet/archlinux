# Arch Installation Part1 (Base System)

<br>

## POINTERS

[Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide)
[Installation Guide - Fred Bezies](https://github.com/FredBezies/arch-tuto-installation/blob/master/install.md)

<br>

## PREPARE USB INSTALLATION KEY

[USB Flash Installation](https://wiki.archlinux.org/index.php/USB_flash_installation_media)

Note:
- Using dd - This method is recommended due to its simplicity.
- Check what is your device, this will irrevocably destroy all data on /dev/sdx

Find out the name of your USB drive with lsblk (or df). Make sure that it is not mounted.

Run the following command, replacing /dev/sdx with your drive, e.g. /dev/sdb. (Do not append a partition number, so do not use something like /dev/sdb1) 
```bash
# sudo dd bs=4M if=/home/jmb/softwares/archlinux-2020.05.01-x86_64.iso of=/dev/sda status=progress oflag=sync
```

Check that you have the USB disk correctly partionned:
```bash
# lsblk
sda                       8:0    1  29,3G  0 disk  
├─sda1                    8:1    1   652M  0 part  
└─sda2                    8:2    1    64M  0 part  
#
```

Boot on the USB key.

<br>

## PRE-INSTALLATION

### Set the keyboard layout

The default console keymap is US. Available layouts can be listed with:

```bash
# ls /usr/share/kbd/keymaps/**/*.map.gz
```

To modify the layout, append a corresponding file name to loadkeys(1), omitting path and file extension. For example, to set a German keyboard layout:
```bash
# loadkeys de-latin1
```
Console fonts are located in /usr/share/kbd/consolefonts/ and can likewise be set with setfont(8).


### Verify the boot mode

If UEFI mode is enabled on an UEFI motherboard, Archiso will boot Arch Linux accordingly via systemd-boot. To verify this, list the efivars directory:

```bash
# ls /sys/firmware/efi/efivars
```

### Connect to the internet
To set up a network connection, go through the following steps

```bash
# ip link
# ip addr
```

Note: The installation image enables dhcpcd (dhcpcd@interface.service) for wired network devices on boot. The connection may be verified with ping:

```bash
# ping archlinux.org
```


### Update the system clock

Use timedatectl(1) to ensure the system clock is accurate:
```bash
# timedatectl set-ntp true
```

To check the service status, use
```bash
#  timedatectl status
```

<br>

## PARTITION AND FORMAT THE DISK - BIOS

When recognized by the live system, disks are assigned to a block device such as /dev/sda or /dev/nvme0n1. To identify these devices, use lsblk or fdisk. 

The Unified Extensible Firmware Interface (UEFI or EFI for short) is a new model for the interface between operating systems and firmware. It provides a standard environment for booting an operating system and running pre-boot applications.

It is distinct from the commonly used "MBR boot code" method followed for BIOS systems. See Arch boot process for their differences and the boot process using UEFI. To set up UEFI boot loaders, see Arch boot process#Boot loader. 

In summary, 2 possible modes:
- BIOS with MBR
-  UEFI with GPT

For more information: [Arch Boot Process](https://wiki.archlinux.org/index.php/Arch_boot_process)

### Partition the disks (BIOS mode)

Use fdisk or even better cfdisk with the recommended Partitions:

|    Partition   |     Mount     |      Size       |   File System   |
|--------------- |---------------|-----------------|-----------------|
|  /dev/vda1     |   /boot       |  200M           |   ext4          |
|  /dev/vda2     |               |  1G             |   swap          |
|  /dev/vda3     |  /            |  25G min        |   ext4          |
|  /dev/vda4     |   /home       |  Rest of disk   |   ext4          |

<br>

```bash
# fdisk /dev/sda
```
or cfdisk which is easier.
```bash
# cfdisk /dev/sda
```

With cfdisk, pick option: dos
Configure /boot as bootable

With fdisk:
- P to print partitions (display)
- N for new partition

Create 4 partitions with:
- Partition1 => +200M (boot)
- Partition2 => +1G (swap)
- Partition3 => +25G (root)
- Partition4 => Rest (home)

With:
- N for new partition
- P for primary

then: W to write partitions

Check partitions
```bash
# lsblk 
```


### Format the partitions (BIOS mode)

Once the partitions have been created, each must be formatted with an appropriate file system.

```bash
# mkfs.ext4 /dev/sda1
# mkfs.ext4 /dev/sda3
# mkfs.ext4 /dev/sda4
```

If you created a partition for swap, initialize it with mkswap:
```bash
# mkswap /dev/sda2
# swapon /dev/sdb2
```


### Mount the file systems (BIOS mode)

```bash
# mount /dev/sda3 /mnt
# mkdir /mnt/home
# mkdir /mnt/boot
# mount /dev/sda1 /mnt/boot
# mount /dev/sda4 /mnt/home
```



## PARTITION AND FORMAT THE DISK - UEFI MODE

### Partition the disks (UEFI mode)

A GPT partition is required to boot in UEFI mode. Use tool: cgdisk

|    Partition   |     Mount        |       Size       |  File System |
|----------------|------------------|:-----------------|:-------------|
|  /dev/vda1     |   /              |  20G min         |   ext4       |
|  /dev/vda2     |   /boot/efi      |  128M            |   fat32      |
|  /dev/vda3     |                  |  1G min          |   swap       |
|  /dev/vda4     |   /home          |  Rest of disk    |   ext4       |

<br>

Note:
- /boot/efi partition is labelled EF00. 
- For swap, this is 8200


### Create Partitions (UEFI mode)

Use cgdisk with /dev/vba
```bash
# gdisk /dev/sda
```
or
```bash
# cgdisk /dev/sda
```

Partition1 - Boot
- First Sector: <enter>
- Last sector: +512M
- Hex code for GUID: EF00 
- => (EFI System)

Partition2 - Linux file system
- First Sector: <enter>
- Last sector: +512M
- Hex code for GUID: <enter> 
- => (Linux filesystem)

then 'w' - this will also create the GPT data.


### Format the partitions (UEFI mode)

Once the partitions have been created, each must be formatted with an appropriate file system.
```bash
# mkfs.ext4 /dev/vda1
# mkfs.fat -F32 /dev/vda2
# mkfs.ext4 /dev/vda4
```

If you created a partition for swap, initialize it with mkswap:
```bash
# mkswap /dev/sda3
# swapon /dev/sda3
```

### Mount the file systems (UEFI mode)

```bash
# mount /dev/sda1 /mnt
# mkdir /mnt/{boot,boot/efi,home}
# mount /dev/sda2 /mnt/boot/efi
# mount /dev/sda4 /mnt/home
```


<br>


## INSTALLATION

### Select the mirrors

Packages to be installed must be downloaded from mirror servers, which are defined in /etc/pacman.d/mirrorlist. On the live system, all mirrors are enabled, and sorted by their synchronization status and speed at the time the installation image was created.

The higher a mirror is placed in the list, the more priority it is given when downloading a package. You may want to edit the file accordingly, and move the geographically closest mirrors to the top of the list, although other criteria should be taken into account.
This file will later be copied to the new system by pacstrap, so it is worth getting right.

Mettre à jour
```bash
# sudo pacman -Syyu
```


### Install essential packages

```bash
# pacstrap /mnt base base-devel linux linux-firmware vim dhcpcd
# pacstrap /mnt zip unzip p7zip mc alsa-utils syslog-ng mtools dosfstools lsb-release ntfs-3g exfat-utils bash-completion
```

Note:
- exfat-utils pour la prise en charge des cartes SD de grande capacité.
- Si vous êtes sur un pc portable, l’ajout de tlp est conseillé pour améliorer l’autonomie de la batterie. Plus d’info sur cette page [ArchWiki TLP](https://wiki.archlinux.org/index.php/TLP)


Bootloader

For a boot using BIOS:
```bash
# pacstrap /mnt grub os-prober
```

For a boot using UEFI:
```bash
# pacstrap /mnt grub os-prober efibootmgr
```


<br>


## CONFIGURE THE SYSTEM

### Generate fstab
 
Generate /etc/fstab file using UUID labels (and not /dev/sda names)
```bash
# genfstab -U /mnt >> /mnt/etc/fstab
```


### Chroot

Change root into the new system:
```bash
# arch-chroot /mnt 
```


### Configure Timezone

In /usr/share/zoneinfo
```bash
# ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime 
```

Run hwclock(8) to generate /etc/adjtime:
```bash
# hwclock --systohc --utc
```


### Localization

Edit /etc/locale.gen 
uncomment en_US.UTF-8 and other needed locales. 
Generate the locales by running:
```bash
# locale-gen
```

Create the locale.conf(5) file, and set the LANG variable accordingly:
```bash
# vim /etc/locale.conf 
LANG=en_US.UTF-8
```


### Network Configuration

Configure Host name
```bash
# vim /etc/hostname
```

Add matching entries to hosts:
```
/etc/hosts
127.0.0.1	localhost
::1		localhost
127.0.1.1	myhostname.localdomain	myhostname
```

If the system has a permanent IP address, it should be used instead of 127.0.1.1.



### Root password

Set the root password:
```bash
# passwd
```


### Boot loader

For installation using BIOS mode (Note: This is the disk, not the partition, so no number):
```bash
# grub-install --no-floppy --recheck /dev/sda
```

for installation in UEFI mode :
La première ligne permet de vérifier un point de montage et de l’activer au besoin. La deuxième installe Grub. 
```bash
# mount | grep efivars &> /dev/null || mount -t efivarfs efivarfs /sys/firmware/efi/efivars
# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch_grub --recheck
```

In addition to that - to avoid any startup issue, especially with VirtualBox - add:
```bash
# mkdir /boot/efi/EFI/boot
# cp /boot/efi/EFI/arch_grub/grubx64.efi /boot/efi/EFI/boot/bootx64.efi
```

generate grub config (new from grub 2:2.02-8):
```bash
# grub-mkconfig -o /boot/grub/grub.cfg
```


### Network

Activate NetworkManager:
```bash
# pacman -Syy networkmanager
# systemctl enable NetworkManager
```


### Reboot

Exit the chroot environment by typing exit or pressing Ctrl+d.
```bash
# exit
```

Optionally manually unmount all the partitions with:
```bash
# umount -R /mnt
```

this allows noticing any "busy" partitions, and finding the cause with fuser(1).

Finally, restart the machine by typing
```bash
# reboot
```

Any partitions still mounted will be automatically unmounted by systemd. Remember to remove the installation media and then login into the new system with the root account.



