<h1 align="center">
	<a href="https://github.com/fehawen/arch-guide/blob/master/docs/INSTALL.md">
		<img alt="xps installation" src="https://user-images.githubusercontent.com/36552788/59856089-7df2d100-9376-11e9-906a-cc3f8a6d6001.png" width="250">
	</a>
	<br>
	<br>
	Arch Linux on Dell XPS 13 (9343)
	<br>
	Encrypted installation with LVM on LUKS
	</h1>

## Installation

Set temporary keymap.

<pre>
$ loadkeys sv-latin1
</pre>

Install font and editor for use during installation.

<pre>
$ pacman -Sy neovim terminus-font
</pre>

Change mirrors if needed.

<pre>
$ nvim /etc/pacman.d/mirrorlist
</pre>

Set font for easier read.

<pre>
$ setfont ter-v28n
</pre>

Securely erase the disk.

<pre>
$ shred --verbose --random-source=/dev/urandom --iterations=3 /dev/sda
</pre>

Partition with gdisk.

<pre>
$ gdisk /dev/sda
</pre>

Create an empty GPT partition table.

<pre>
$ o
</pre>

Create a new EFI boot partition.

<pre>
$ n

Partition number (...):
First sector (...):
Last sector (...): +512MiB
Hex code or GUID (...): ef00
</pre>

Create a new Linux Filesystem partition (to be used as the encrypted container).

<pre>
$ n

Partition number (...):
First sector (...):
Last sector (...):
Hex code or GUID (...):
</pre>

Write changes to execute partitioning.

<pre>
$ w
</pre>

Create the LUKS encrypted container.

<pre>
$ cryptsetup --verbose --cipher aes-xts-plain64 --key-size 512 --hash sha512 --iter-time 5000 --use-random luksFormat /dev/sda2
</pre>

Open the encrypted container.

<pre>
$ cryptsetup open --type luks /dev/sda2 cryptroot
</pre>

Create a physical volume on top of the opened LUKS container.

<pre>
$ pvcreate /dev/mapper/cryptroot
</pre>

Create the volume group, and add the previously created physical volume to it.

<pre>
$ vgcreate CryptVolGroup /dev/mapper/cryptroot
</pre>

Create a swap logical volume on the volume group (size: RAM * 1.5).

<pre>
$ lvcreate -L 12G CryptVolGroup -n swap
</pre>

Create a root logical volume on the volume group (size: remaining free space).

<pre>
$ lvcreate -l 100%FREE CryptVolGroup -n root
</pre>

Format root logical volume filesystem.

<pre>
$ mkfs.ext4 /dev/CryptVolGroup/root
</pre>

Format swap logical volume filesystem.

<pre>
$ mkswap /dev/CryptVolGroup/swap
</pre>

Mount root filesystem.

<pre>
$ mount /dev/CryptVolGroup/root /mnt
</pre>

Initialize swap.

<pre>
$ swapon /dev/CryptVolGroup/swap
</pre>

Create a filesystem for the boot partition.

<pre>
$ mkfs.fat -F32 /dev/sda1
</pre>

Create the boot directory.

<pre>
$ mkdir /mnt/boot
</pre>

Mount the boot partition.

<pre>
$ mount /dev/sda1 /mnt/boot
</pre>


Install base system.

<pre>
$ pacstrap /mnt base base-devel
</pre>

Generate an fstab.

<pre>
$ genfstab -U -p /mnt >> /mnt/etc/fstab
</pre>

If necessary, edit fstab file, changing relatime on all non-boot partitions to noatime (reduces wear if using an SSD).

<pre>
$ nvim /mnt/etc/fstab
</pre>

Get root access to the system itself.

<pre>
$ arch-chroot /mnt
</pre>

Install additional packages.

<pre>
$ pacman -S wireless_tools broadcom-wl linux-headers intel-ucode sudo dialog wpa_supplicant neovim git
</pre>

Set timezone.

<pre>
$ rm /etc/localtime
$ ln -sv /usr/share/zoneinfo/Europe/Stockholm /etc/localtime
</pre>

Sync hardware clock.

<pre>
$ timedatectl set-ntp true
</pre>

Set hardware clock.

<pre>
$ hwclock --systohc --utc
</pre>

Set locale.

<pre>
$ nvim /etc/locale.gen
</pre>

Uncomment preferred option.

<pre>
en_US.UTF-8 UTF-8
</pre>

Generate locale file.

<pre>
$ locale-gen
</pre>

Load selected locale on startup.

<pre>
$ echo LANG=en_US.UTF-8 > /etc/locale.conf
</pre>

Set permanent keymap for keyboard layout.

<pre>
$ nvim /etc/vconsole.conf
</pre>

Insert preferred keymap and console font.

<pre>
KEYMAP=sv-latin1
FONT=ter-v28n
</pre>

UPDATE:
Set lang and keymaps using `localectl` instead.

<pre>
localectl set-locale LANG="en_US.UTF-8"
localectl set-keymap se
localectl set-x11-keymap se
</pre>

Create root password.

<pre>
$ passwd
</pre>

Set host name.

<pre>
$ echo <b>hostname</b> > /etc/hostname
</pre>

Create a non-root user.

<pre>
$ useradd -m -g users -G wheel -s /bin/bash <b>username</b>
</pre>

Create password for new user.

<pre>
$ passwd <b>username</b>
</pre>

Enable sudo commands without password.

<pre>

$ sudo EDITOR=nvim visudo
</pre>

Uncomment the following line.

<pre>
%wheel ALL=(ALL) NOPASSWD: ALL
</pre>

Lock the root account.

<pre>
$ passwd -l root
</pre>

Install bootloader.

<pre>
bootctl install
</pre>

Check that everything looks alright.

<pre>
$ ls /boot
</pre>

Configure init file.

<pre>
$ nvim /etc/mkinitcpio.conf
</pre>

Add the necessary hooks.

<pre>
$ HOOKS=(base <b>udev</b> autodetect <b>keyboard keymap consolefont</b> modconf block <b>encrypt lvm2</b> filesystems fsck)
</pre>

Setup loader configuration.

<pre>
$ nvim /boot/loader/loader.conf
</pre>

Add configuration details.

<pre>
default arch
timeout 4
console-mode max
editor no
</pre>

Setup arch configuration entry.

<pre>
$ nvim /boot/loader/entries/arch.conf
</pre>

Add arch entry configuration details.

<pre>
title Arch Linux
linux /vmlinuz-linux
initrd /intel-ucode.img
initrd /initramfs-linux.img
options rw cryptdevice=UUID=<b>XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX</b>:cryptroot root=/dev/CryptVolGroup/root

# Read in /dev/sda2 PARTUUID
:read ! blkid -s UUID -o value /dev/sda2
</pre>

Generate init file.

<pre>
$ mkinitcpio -p linux
</pre>

Exit the chroot session.

<pre>
$ exit
</pre>

Unmount the file system.

<pre>
$ umount -R /mnt
</pre>

Reboot, remove the Live USB, enjoy.

<pre>
$ reboot
</pre>
