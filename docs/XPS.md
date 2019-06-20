<h1 align="center">
	<a href="https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md">
		<img alt="xps installation" src="https://user-images.githubusercontent.com/36552788/59856089-7df2d100-9376-11e9-906a-cc3f8a6d6001.png" width="250">
	</a>
	<br>
	<br>
	Arch Linux on Dell XPS 13 (9343)
	</h1>

## About

Installation guide for Arch Linux on a Dell XPS 13 (9343) using:

* `UEFI` firmware interface
* `systemd-boot` bootloader
* `GPT` partitioning table
* `LUKS` encrypted `LVM` partition for `/root`, `/home` and `swap`

## Table of Contents

1. [Issues](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#1-issues)
2. [Bootup](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#2-bootup)
	* 2.1. [UEFI](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#21-uefi)
	* 2.2. [Keymap](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#22-keymap)
	* 2.3. [Font](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#23-font)
	* 2.4. [Internet](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#24-internet)
		* 2.4.1. [Ethernet](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#241-ethernet)
		* 2.4.2. [Wifi](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#242-wifi)
			* 2.4.2.1. [wpa_supplicant](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#2421-wpa_supplicant)
			* 2.4.2.2. [wifi-menu](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#2422-wifi-menu)
3. [Partitioning](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#3-partitioning)
4. [EFI](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#4-efi)
5. [LUKS](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#5-luks)
6. [LVM](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#6-lvm)
7. [Mount](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#7-mount)
8. [Base](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#8-base)
9. [fstab](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#9-fstab)
10. [Configuration](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#10-configuration)
11. [Bootlader](https://github.com/fehawen/arch-guide/blob/master/docs/XPS.md#11-bootloader)

## 1. Issues

The Dell DW1560 802.11ac adapter (based on the Broadcom BCM4352 chip) requires `broadcom-wl` or `broadcom-wl-dkms`, and also `linux-headers` (even though it's listed as an optional dependency).

See [Arch Wiki - Dell XPS 13 (9343)](https://wiki.archlinux.org/index.php/Dell_XPS_13_(9343)) and [Arch Wiki - Broadcom wireless](https://wiki.archlinux.org/index.php/Broadcom_wireless) for more information.

## 2. Bootup

### 2.1. UEFI

To make sure, check that the system uses `UEFI`, which it should.

If the command returns a list of files, all is well.

<pre>
$ ls /sys/firmware/efi/efivars
# or
$ efivar -l
</pre>

### 2.2. Keymap

Make typing commands a bit easier by setting a temporary keymap (Swedish layout for example) for the current session.

Permanent keymap will be set later on.

<pre>
$ loadkeys sv-latin1
</pre>

For other options, list available keymaps in `/usr/share/kbd/keymaps/`.

<pre>
$ ls /usr/share/kbd/keymaps/
</pre>

### 2.3. Font

The screen resolution on the Dell XPS might make the font look incredibly small, which we can fix temporarily during the installation.

<pre>
# It's ugly, bit the best we can do on this screen, for now
$ setfont sun12X22
</pre>

Or list available fonts for options.

<pre>
$ ls /usr/share/kbd/consolefonts
</pre>

<pre>
$ fc-list
</pre>

### 2.4. Internet

Depending on the situation, hardware, prerequisites and preference, set up a connection using either `ethernet` or `wi-fi`.

If you for some reason – adaptor issues, lack of ports, forgot to pay the internet bill, stuck in the woods, whatever – can't connect an ethernet cable or don't have access to wifi, tethering from your phone over USB might be an option.

Thethering should work fine, at least if it's an **Android** phone (or so I've heard). If it's an **iPhone** you're currently sporting however, then perhaps you ought to read [this](https://wiki.archlinux.org/index.php/IPhone_tethering).

#### 2.4.1. Ethernet

Detect your network interface, called something like `enp0s25` or whatever.

<pre>
$ ip link
</pre>

If needed, connect the interface to ze internetz using `dhcpcd`. Ping first to check.

<pre>
$ dhcpcd <interface>
</pre>

Ping something to verify your connection, like ping Google 5 times.

<pre>
$ ping 5 8.8.8.8
</pre>

*Or perhaps the **-c** flag for count is required, I can never remember.*

<pre>
$ ping -c 5 8.8.8.8
</pre>

#### 2.4.2. Wifi

##### 2.4.2.1. wpa_supplicant

Check the wireless card status.

<pre>
$ rfkill list
</pre>

If it's blocked, try to unblock it.

<pre>
$ rfkill unblock wifi
</pre>

Find name of wireless interface, usually something like `wlp2s0`, `wlp3s0b1` or whatevs.

<pre>
$ iwconfig
</pre>

Scan for wireless networks, unless you already know yours.

<pre>
$ iwlist <b>interface</b> scan
</pre>

*Or use.*

<pre>
$ iwlist <b>interface</b> scan | grep ESSID
</pre>

Create a `wpa_supplicant.conf` file with `wpa_passphrase`.

<pre>
$ wpa_passphrase <b>ESSID</b> <b>passphrase</b> | tee /etc/wpa_supplicant.conf
</pre>

Connect to the wireless access point.

*Running in foreground.*

<pre>
$ wpa_supplicant -c /etc/wpa_supplicant.conf
</pre>

*Running in background.*

<pre>
$ wpa_supplicant -B -c /etc/wpa_supplicant.conf
</pre>

Check wireless interface status again, to verify.

<pre>
$ iwconfig
</pre>

Obtain private IP address from DHCP server.

<pre>
$ dhclient <b>interface</b>
</pre>

Show obtained private IP address.

<pre>
$ ifconfig <b>interface</b>
</pre>

Use `-r` flag to release private IP address, when/if needed.

<pre>
$ dhclient <b>interface</b> -r
</pre>

##### 2.4.2.2. wifi-menu

Connecting to a wireless network with `wifi-menu` is self-explanatory.

<pre>
$ wifi-menu
</pre>

## 3. Partitioning

Use `gdisk` for partitioning.

<pre>
$ gdisk /dev/sda
# or, you know, might be something like
$ gdisk /dev/nvme0n1
</pre>

Create an empty `GPT` partition table.

<pre>
$ o
</pre>

Create a new `EFI` partition (512 MB).

<pre>
$ n

# Partition number (...):
# First sector (...):
Last sector (...): +512MiB

Hex code or GUID (...): ef00
</pre>

Create a new Linux `LVM` partition (REMAINING SPACE).

<pre>
$ n

# Partition number (...):
# First sector (...):
# Last sector (...):

Hex code or GUID (...): 8e00
</pre>

Write changes, i.e. execute partitioning.

<pre>
$ w
</pre>

## 4. EFI

Create `FAT32` file system for `EFI` partition.

<pre>
# EFI partition created first, so it's 1
$ mkfs.fat -F32 /dev/sda1
</pre>

## 5. LUKS

Encrypt Linux `LVM` partition with `LUKS`.

<pre>
# LVM partition created second, so it's 2
$ cryptsetup luksFormat /dev/sda2
</pre>

Open encrypted partition to enable installation, naming it **lvm** as example here and for the remaining steps.

<pre>
$ cryptsetup open --type luks /dev/sda2 <b>lvm</b>
</pre>

Check that it exists.

<pre>
$ ls /dev/mapper/lvm
</pre>

Create physical volume on top of `/dev/mapper/lvm`.

<pre>
$ pvcreate /dev/mapper/lvm
</pre>

Create volume group, naming it **volume** as example here and for the remaining steps.

<pre>
$ vgcreate <b>volume</b> /dev/mapper/lvm
</pre>

Create logical volume for `swap`.

<pre>
# 12 GB (150% of 8 GB RAM) SWAP in the 'volume' group, named 'swap', which results in 'dev/mapper/volume-swap'
$ lvcreate -L12G volume -n swap
</pre>

Create logical volume for `root`.

<pre>
# 30 GB root volume in the 'volume' group, named 'root', which results in 'dev/mapper/volume-root'
$ lvcreate -L30G volume -n root
</pre>

Create logical volume for `home`.

<pre>
# Remaining space home volume in the 'volume' group, named 'home', which results in 'dev/mapper/volume-home'
$ lvcreate -l 100%FREE volume -n home
</pre>

## 6. LVM

Make SWAP for `swap`.

<pre>
$ mkswap /dev/mapper/volume-swap
</pre>

Make EXT4 file system for `root`.

<pre>
$ mkfs.ext4 /dev/mapper/volume-root
</pre>

Make EXT4 file system for `home`.

<pre>
$ mkfs.ext4 /dev/mapper/volume-home
</pre>

## 7. Mount

Mount `root` to `/mnt`.

<pre>
$ mount /dev/mapper/volume-root /mnt
</pre>

Make `/mnt/home` directory.

<pre>
$ mkdir /mnt/home
</pre>

Make `boot/mnt/boot` directory.

<pre>
$ mkdir /mnt/boot
</pre>

Mount `home` to `/mnt/home`

<pre>
$ mount /dev/mapper/volume-home /mnt/home
</pre>

Mount the `EFI` boot partition to `/mnt/boot`

<pre>
# Note that we're using our EFI /dev/sda1 partition here, as it's only our encrypted LVM partition that keeps holds the volumes 'root' and 'home'
$ mount /dev/sda1 /mnt/boot
</pre>

Acivate `swap`.

<pre>
$ swapon /dev/mapper/volume-swap
</pre>

## 8. Base

Install the base system, including `base` and `base-devel`, along with `neovim` for convenience.

<pre>
$ pacstrap /mnt base base-devel neovim
</pre>

## 9. fstab

Take a peek to check that everything looks all right first.

<pre>
$ genfstab /mnt
</pre>

Output `fstab` to `/mnt/etc/fstab`, with `-p` flag (default behaviour).

<pre>
$ genfstab -p /mnt >> /mnt/etc/fstab
</pre>

## 10. Configuration

Get `root` access to the system itself.

<pre>
$ arch-chroot /mnt
</pre>

Install additional packages, noting that `broadcom-wl`and `linux-headers` are required for the `Broadcom BCM4352` based `Dell DW1560 802.11ac` adapter to work, and `ìntel-ucode``enables Intel microcode updates.

<pre>
$ pacman -S networkmanager broadcom-wl linux-headers intel-ucode sudo
</pre>

Tell `systemd` to automatically start `networkmanager` on startup.

<pre>
$ systemctl enable NetworkManager
</pre>

Set timezone by symlinking the desired `zone` with `subzone` to `/etc/localtime`.

<pre>
# The -v flag is just for verbose output
$ ln -sv /usr/share/zoneinfo/<b>zone</b>/<b>zubsone</b> /etc/localtime

# You may get an error that the file already exists,
# in which case you can use the <b>-f</b> flag for force
$ ln -sfv /usr/share/zoneinfo/<b>zone</b>/<b>zubsone</b> /etc/localtime

# Or delete it first, then symlink
$ rm /etc/localtime
$ ln -sv /usr/share/zoneinfo/<b>zone</b>/<b>zubsone</b> /etc/localtime
</pre>

If needed, look through `/usr/share/zoneinfo` first for `zone` and `subzone` options.

<pre>
$ cd /usr/share/zoneinfo && ls
# or
$ ls /usr/share/zoneinfo
</pre>

Set hardware clock from current system time.

<pre>
$ hwclock --systohc --utc
</pre>

Set locale by uncommenting preferred line in `/etc/locale.gen`.

<pre>
$ nvim /etc/locale.gen
</pre>

Here, you'll most likely only need `UTF-8`, unless for some reason you also require `ISO`.

<pre>
# en_US.UTF-8 UTF-8
# en_US ISO-8859-1
...
# Uncomment preferred choice, e.g. Swedish for example
sv_SE.UTF-8 UTF-8
# sv_SE ISO-8859-1
</pre>

Generate locale file.

<pre>
$ locale-gen
</pre>

Set selected locale upon boot by writing it to `/ec/locale.conf`.

<pre>
# Either like this
$ locale > /etc/locale.conf

# Or like this
$ echo LANG=sv_SE.UTF-8 > /etc/locale.conf

# Or edit /etc/locale.conf manually in e.g. nvim
</pre>

Set permanent keymap for keyboard layout (we did this before, but temporarily), by editing `/etc/vconsole.conf`.

<pre>
$ nvim /etc/vconsole.conf
</pre>

Edit the necessary line with your preferred layout (again, Swedish for example).

<pre>
$ KEYMAP=sv-latin1
</pre>

Create a `root` password.

<pre>
$ passwd
</pre>

Set host name.

<pre>
$ echo <b>host_name</b> > /etc/hostname
</pre>

Create a non-root user.

<pre>
$ useradd -m -g users -G wheel -s /bin/bash <b>username</b>
</pre>

Create password for new user.

<pre>
$ passwd <b>username</b>
</pre>

Edit `/etc/sudoers` to allow `sudo` commands without being asked for password all the time. And yes, this may or may not be a good idea.

<pre>
# The file is readonly, hence the following command
$ sudo EDITOR=nvim visudo

# Uncomment this line, somewhere at the end of that file
%wheel ALL=(ALL) NOPASSWD: ALL
</pre>

Next, lock the `root` account via `passwd`.

<pre>
$ passwd -l root
</pre>

To unlock it, whenever needed, use the `-u` flag.

<pre>
$ sudo passwd -u root
</pre>

Since we're using LUKS disk encryption and LVM, we need to change the `initramfs` to give us keyboard access (for typing our password) before the system mount the devices.

<pre>
$ nvim /etc/mkinitcpio.conf
</pre>

Search for the variable `HOOKS` inside `/etc/mkinitcpio.conf` and rearrange the order so that `keyboard` is listead before `filesystems`, and adding `encrypt` and `lvm2` (also before `filesystems`)

<pre>
...
HOOKS="... keyboard encrypt lvm2 filesystems ..."
...
</pre>

Next, regenerate our updated `mkinitcpio.conf`, which will regenrate our `initramfs` as well.

<pre>
$ mkinitcpio -p linux
</pre>

## 11. Bootloader

Install the `systemd` bootloader.

<pre>
$ bootctl --path=/boot install
</pre>

Configure the loader.

<pre>
$ nvim /boot/loader/loader.conf
</pre>

<pre>
default arch
timeout 4
console-mode max
editor no
</pre>

Create the `arch` entry.

<pre>
$ nvim /boot/loader/arch.conf
</pre>

<pre>
title Arch Linux
linux /vmlinuz-linux
initrd /intel-ucode.img
initrd /initramfs-linux.img
options cryptdevice=UUID=<b>XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX</b>:volume root=/dev/mapper/volume-root quiet rw

# Get the UUID from within <em>vim</em> or <em>nvim</em>.
:read ! blkid /dev/sda2

# Or like this.
:read ! cryptsetup luksUUID /dev/sda2

# <em>/dev/sda2</em> is our LUKS formatted LVM partition.
# <em>/dev/sda1</em> is our boot partition.
</pre>

Exit the `chroot` session.

<pre>
$ exit
</pre>

Unmount the file system, with `-R` flag for recursive to unmount what's on `/mnt` along with everything else.

<pre>
$ umount -R /mnt
</pre>

Reboot, remove Live USB while booting, and we should be

<pre>
$ reboot
</pre>

Done.
