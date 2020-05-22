# Bootloader (BIOS, UEFI)


## Introduction

The Unified Extensible Firmware Interface (UEFI or EFI for short) is a new model for the interface between operating systems and firmware. It provides a standard environment for booting an operating system and running pre-boot applications.

It is distinct from the commonly used "MBR boot code" method followed for BIOS systems. See Arch boot process for their differences and the boot process using UEFI. To set up UEFI boot loaders, see Arch boot process#Boot loader. 

In summary, 2 possible modes:
- BIOS with MBR
-  UEFI with GPT

For more information: [Arch Boot Process](https://wiki.archlinux.org/index.php/Arch_boot_process)


<br>


## Bootloader

Options:
- GRUB
- bootctl is suggested as being much better than GRUB (or less bloated)
- rEFInd can also used instead of bootctl (fancier looking) 

[GRUB](https://wiki.archlinux.org/index.php/GRUB)
[GRUB/EFI](https://wiki.archlinux.org/index.php/GRUB/EFI_examples)
[systemd-boot - bootctl](https://wiki.archlinux.org/index.php/Systemd-boot)


<br>


## Using bootctl with UEFI

To install the systemd-boot EFI boot manager, first make sure the system has booted in UEFI mode and that UEFI variables are accessible. This can be checked by running the command efivar --list or, if efivar is not installed, by doing ls /sys/firmware/efi/efivars (if the directory exists, the system is booted in UEFI mode).

Install in less than 10min using UEFI
https://www.youtube.com/watch?v=DfC5hgdtbWY



<br>


## Create Partitions

```
# gdisk /dev/sda
```
or
```
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

Note:
- /boot/efi partition is labelled EF00. 
- For swap, this is 8200


<br>


## Format Partitions

```
# mkfs.vfat /dev/sda1
# mkfs.ext4 /dev/sda2
```


<br>


## mount Partitions

```bash
# mount /dev/sda2 /mnt
# mkdir /mnt/boot
# mount /dev/sda1 /mnt/boot
```


<br>

## Install system

```bash
# pacstrap /mnt
```



## Chroot to /mnt

```bash
# arch-chroot /mnt
```


<br>


## Install bootloader


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

Configure loader.conf
```bash
# cd loader
# vim loader.conf
```

remove all default lines there.

And add:
```
default arch.conf
timeout 4
```

Configure arch.conf
```
# cd entries
# vim arch.conf
```

And add:
```
title Arch Linux
linux /vmlinuz-linux
initrd /initramfs-linux.img
options root=PARTUUID=824523....  rw  (put in there /dev/sda2 PARTUUID)
```

Tip:
- use vim
- read the output of the command blkid
- :r !blkid

reboot






[Q] You didn't have to create a GPT partition table? You just went into gdisk and it was already present
[A] I setup the GPT partition table using gdisk. I think I ran through the steps before hence the reason why it was present to begin with. But using gdisk like I did should create one if you don't have one.




