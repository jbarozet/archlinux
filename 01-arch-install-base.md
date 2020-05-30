# Arch Installation Part1 (Base System)

<br>

## POINTERS

[Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide)
[How to install Arch Linux](https://wiki.learnlinux.tv/index.php/How_to_Install_Arch_Linux)

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

To modify the layout, append a corresponding file name to loadkeys(1), omitting path and file extension. For example, to set a French keyboard layout:
```bash
# loadkeys fr
```
Console fonts are located in /usr/share/kbd/consolefonts/ and can likewise be set with setfont(8).


### Verify the boot mode

If UEFI mode is enabled on an UEFI motherboard, Archiso will boot Arch Linux accordingly via systemd-boot. To verify this, list the efivars directory:

```bash
# ls /sys/firmware/efi/efivars
```

### Connect to the internet

The installation image enables dhcpcd (dhcpcd@interface.service) for wired network devices on boot. 

If using a wireless interface, configure wireless settings. Use wifi-menu to generate the profile file in /etc/netctl/.
```bash
# wifi-menu
```

Check that you have an IP address:
```bash
# ip addr
```

The connection may be verified with ping:
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

When recognized by the live system, disks are assigned to a block device such as /dev/sda, /dev/vda or /dev/nvme0n1. To identify these devices, use lsblk or fdisk. 

The Unified Extensible Firmware Interface (UEFI or EFI for short) is a new model for the interface between operating systems and firmware. It provides a standard environment for booting an operating system and running pre-boot applications.

It is distinct from the commonly used "MBR boot code" method followed for BIOS systems. See Arch boot process for their differences and the boot process using UEFI. To set up UEFI boot loaders, see Arch boot process#Boot loader. 

In summary, 2 possible modes:
- BIOS with MBR
- UEFI with GPT

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

Create partitions with cfdisk.
```bash
# cfdisk /dev/sda
```

Steps:
- Pick option: dos
- Configure /boot as bootable
- Create 4 primary partitions with:
  * Partition1 => 200M (boot)
  * Partition2 => 1G (swap)
  * Partition3 => 25G (root)
  * Partition4 => Rest (home)

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



## PARTITION AND FORMAT THE DISK - UEFI MODE

When recognized by the live system, disks are assigned to a block device such as /dev/sda, /dev/vda or /dev/nvme0n1. To identify these devices, use lsblk or fdisk. My Intel NUC: /dev/nvme0n1.

The Unified Extensible Firmware Interface (UEFI or EFI for short) is a new model for the interface between operating systems and firmware. It provides a standard environment for booting an operating system and running pre-boot applications.

It is distinct from the commonly used "MBR boot code" method followed for BIOS systems. See Arch boot process for their differences and the boot process using UEFI. To set up UEFI boot loaders, see Arch boot process#Boot loader. 

In summary, 2 possible modes:
- BIOS with MBR
-  UEFI with GPT
-  
When recognized by the live system, disks are assigned to a block device such as /dev/sda, /dev/vda or /dev/nvme0n1. To identify these devices, use lsblk or fdisk.


### Partition the disks (UEFI mode)

A GPT partition is required to boot in UEFI mode. Use tool: cgdisk

|    Partition   |     Mount     |      Size       |   File System   | Hex Code for GUID  |
|--------------- |---------------|-----------------|-----------------|---------------------
|  /dev/vda1     |   /boot/efi   |  512M           |     fat32       |        EF00        |
|  /dev/vda2     |               |  4G             |     swap        |        8200        |
|  /dev/vda3     |   /           |  25G min        |     ext4        |        8300        |
|  /dev/vda4     |   /home       |  Rest of disk   |     ext4        |        8300        |

<br>

Note:
- /boot/efi partition is labelled EF00. 
- For swap, this is 8200
- For linux, this 8300


### Create Partitions (UEFI mode)

Use cgdisk with /dev/nvme0n1
```bash
# cgdisk /dev/sda
```

Resulting partitions for Intel NUC
- partition1 = /dev/nvme0n1p1
- partition2 = /dev/nvme0n1p2
- partition3 = /dev/nvme0n1p3
- partition4 = /dev/nvme0n1p4
 
Writing partitions will also create the GPT data.

Check partitions
```bash
# lsblk 
```


### Format the partitions (UEFI mode)

Format partitions:
```bash
# mkfs.fat -F32 /dev/nvme0n1p1
# mkfs.ext4 /dev/nvme0n1p3
# mkfs.ext4 /dev/nvme0n1p4
```

If you created a partition for swap, initialize it with mkswap:
```bash
# mkswap /dev/nvme0n1p2
# swapon /dev/nvme0n1p2
```

<br>


## MOUNT PARTITIONS

Pick BIOS or UEFI mode.

### Mount the file systems (BIOS mode)

```bash
# mount /dev/sda3 /mnt
# mkdir /mnt/home
# mkdir /mnt/boot
# mount /dev/sda1 /mnt/boot
# mount /dev/sda4 /mnt/home
```

### Mount the file systems (UEFI Mode)

```bash
# mount /dev/nvme0n1p3 /mnt
# mkdir /mnt/{boot,boot/efi,home}
# mount /dev/nvme0n1p1 /mnt/boot/efi
# mount /dev/nvme0n1p4 /mnt/home
```



<br>


## INSTALLATION

### Select the mirrors

Packages to be installed must be downloaded from mirror servers, which are defined in /etc/pacman.d/mirrorlist. On the live system, all mirrors are enabled, and sorted by their synchronization status and speed at the time the installation image was created.

The higher a mirror is placed in the list, the more priority it is given when downloading a package. You may want to edit the file accordingly, and move the geographically closest mirrors to the top of the list, although other criteria should be taken into account.
This file will later be copied to the new system by pacstrap, so it is worth getting right.


### Install essential packages

Install base system
```bash
# pacstrap /mnt base base-devel
```
Install linux (use linux-tls if you want LTS kernel. You can also install both if you want to have a choice at boot)
```bash
# pacstrap /mnt linux linux-firmware
```
Install add-ons
```bash
# pacstrap /mnt vim exfat-utils bash-completion
```

Note:
- for high capacity SD card: add exfat-utils.
- For laptops: tlp is used to improve battery life. More on [ArchWiki TLP](https://wiki.archlinux.org/index.php/TLP)


Install Bootloader

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

To have the frennch keyboard, create file /etc/vconsole.conf:
```
KEYMAP=fr-latin9
FONT=eurlatgr
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


### Boot loader - Using GRUB

For installation using BIOS mode (Note: This is the disk, not the partition, so no number):
```bash
# grub-install --no-floppy --recheck /dev/sda
```

For installation using UEFI mode:
```bash
# mount | grep efivars &> /dev/null || mount -t efivarfs efivarfs /sys/firmware/efi/efivars
# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
```

Generate grub config (new from grub 2:2.02-8):
```bash
# grub-mkconfig -o /boot/grub/grub.cfg
```




### Option - Boot loader - Using bootctl (only for UEFI mode)

[systemd-boot - bootctl](https://wiki.archlinux.org/index.php/Systemd-boot)

```bash
# bootctl install 
```

Will install 
- /boot/EFI
- /boot/EFI/systemd
- /boot/EFI/BOOT
- /boot/loader
- /boot/loader/entries

```bash
# cd /boot
```
Check that linux is there (EFI, intramfslinux, vmlinuz-linux, loader)

Loader configuration
```bash
# cd loader
# vim loader.conf
```

remove all default lines there.

And add:
```
default arch.conf
timeout 4
console-mode max
editor   no
```

Adding loaders:
```
# cd entries
# vim arch.conf
```

And add:
```
title Arch Linux
linux /vmlinuz-linux
initrd /initramfs-linux.img
options root=PARTUUID=824523....  rw  (put in there /dev/vda3 PARTUUID)
```

Tip:
- use vim
- read the output of the command blkid
- :r !blkid



### Network

Activate NetworkManager:
```bash
# pacman -Syy networkmanager
# systemctl enable NetworkManager
```



### Couple of additional packages

Install CPU Microde files (AMD CPU)
```bash
# pacman -S amd-ucode
```

Install CPU Microde files (Intel CPU)
```bash
# pacman -S intel-ucode
```

Install Xorg if you plan on having a GUI
```bash
# pacman -S xorg-server
```


### Video

Steps:
- Install drivers for video cards. xf86-video-vesa package includes a lot of drivers.
- Check this for more information: https://wiki.archlinux.org/index.php/Xorg#Driver_installation

Nvidia 
- check wiki ttps://wiki.archlinux.org/index.php/NVIDIA. 
- optimus : https://wiki.archlinux.org/index.php/NVIDIA_Optimus


```bash
# pacman -S xf86-video-vesa
```

Install Nvidia Driver packages if you have an Nvidia GPU
```bash
# pacman -S nvidia nvidia-utils
```

Note: Install nvidia-lts if you've installed the LTS kernel:
```bash
# pacman -S nvidia-lts
```

Install 3D support for Intel or AMD graphics

If you have an Intel or AMD GPU, install the mesa package:
```bash
# pacman -S mesa
```


VirtualBox - In addition to xf86-video-vesa, install virtualbox-guest-utils
  
```bash
# pacman -S virtualbox-guest-utils xf86-video-vmware
```

Enable kernel modules:
```bash
# systemctl enable vboxservice
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





## APPENDIX - Alternative way for swap

Instead of creating a swap partition, another option is to simply create a file that you can easily expand if needed.

Create swap file
```bash
# fallocate -l 2G /swapfile
# chmod 600 /swapfile
# mkswap /swapfile
```

Back up the /etc/fstab file
```bash
# cp /etc/fstab /etc/fstab.bak
```

Add the swap file to the /etc/fstab file
```bash
# echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab
```

Check the /etc/fstab file to make sure it includes all the right partitions
```bash
# cat /etc/fstab
```


