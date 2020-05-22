# APPENDIX


## Bootloader

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

Install system:
```bash
# pacstrap /mnt zip unzip p7zip mc alsa-utils syslog-ng mtools dosfstools lsb-release ntfs-3g exfat-utils bash-completion
```


## Package install

- Optionally: add telepathy package. Add support for online accounts
