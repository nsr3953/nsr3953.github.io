```
	cat /sys/firmware/efi/fw_platform_size
```
##This returns nothing which tells us we are booting in BIOS mode, which does change how we will do things
##This was the cause of most of my initial trouble because I originally missed there being a difference between EFI and BIOS, and so the steps I was trying to follow were meant for EFI installation

```
	ping archlinux.org
	timedatectl
```
##This is verifying internet connection and checking the time/date service

```
	fdisk -l
	fdisk _/dev/sda
		n
		p
		1
		2048
		20000000
		w
```
##This lists our partitions, then creates a new one, sda1, that is roughly 10 GiB

```

	lsblk -f
	mkfs.ext4 /dev/sda1
	mount /dev/sda1 /mnt
	findmnt
```
##This verifies the partition was created, creates the ext4 filesystem in sda1, mounts it on /mnt, then verifies that it mounted


```
	pacstrap -K /mnt base linux linux-firmware nano vi
	pacstrap -K /mnt networkmanager
```
##This installs fundamental packages in our partition, as well as networkmanager
##One of my worst problems was losing internet connectivity as soon as I rebooted my machine, because I did not have networkmanager installed and could not enable it


```
	genfstab -U /mnt >> /mnt/etc/fstab
	cat /mnt/etc/fstab
```
##This generates our fstab file and verifies the partition description looks correct


```
	arch-chroot /mnt
	ln -sf /usr/share/America/Chicago /etc/localtime
	hwclock --systohc
	locale-gen
```
##This enters us into our chroot environment under sda1
##Then it creates a link for localtime, and does a couple of housekeeping tasks with hwclock and generates our locales 


```
	nano /etc/locale.gen
		Uncomment "en_US.UTF-8 UTF-8"

	nano .etc/locale.conf
		LANG=_en_US.UTF-8_

	export LANG=en_US.UTF-8

	nano /etc/hostname
		archiso

	passwd
```
##This edits our locale files for our region, assigns us a hostname, and resets our passwork


```
	pacman -S grub
	grub-install /dev/sda
	grub-mkconfig -o /boot/grub/grub.cfg
```
##Finally, we install grub as our bootloader
##This part gave me trouble for a while because I did not read that grub-install was supposed to target the disk not the partition, so I repeatedly tried to grub install /dev/sda1 which did not respond kindly

```
	exit 
	reboot
```
##Reboot our VM and login as root

```
	systemctl enable NetworkManager.service
	systemctl start NetworkManager.service
	ping 8.8.8.8
```
##Before anything else, we reenable networkmanager to get internet connection back
##Then we verify that we are back to having internet connection

```
	pacman -S gnome
	systemctl enable gdm.service
	reboot
```
##next we install gnome as our DE and reboot to boot into it


```
	useradd -m -s /bin/bash Noah
	useradd -m -s /bin/bash Codi

	passwd Noah
	passwd Codi

	visudo

	pacman -S zsh
```
##Last, we create necessary users and reset their passwords, edit the sudoers file with visudo to give the users sudo permissions, and install zsh as our alternate shell
