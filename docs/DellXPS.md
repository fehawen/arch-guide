<h1 align="center">
	<a href="https://github.com/fehawen/arch-guide">
		<img alt="arch rice" src="https://user-images.githubusercontent.com/36552788/59663744-f6f3fc00-91af-11e9-9386-bd18df3ae57a.png" width="400">
	</a>
	<br>
	<br>
	Installation guide / config notes
</h1>

<div align="center">
	<h4>
		<a href="./docs/DellXPS.md">Dell XPS</a> |
		<a href="./docs/Config.md">Config</a>
	</h4>
</div>


# Arch Linux on Dell XPS 13 (9343)

## About

Installation guide for Arch Linux on a Dell XPS 13 (9343) using:

* `UEFI` firmware interface
* `systemd-boot` bootloader
* `GPT` partitioning table
* `LUKS` encrypted `LVM` partition for `/root` and `/home`

Yes, yes, I know. RTFM and all that...

Some people might frown upon the fact that I've put together my own little installation guide/note cheathseet here for the world to see (OMG! Open Source!?), instead of reading the [Bible](https://wiki.archlinux.org) from cover to cover as if installing an OS is the only thing ever worth spending time on.

But, I can't be arsed to memorize all the darn commands and steps and options and keep-in-minds and whatnots, and I don't fancy having a gazillion browser tabs open whenever I need to reinstall it, so I'll do what's forbidden and put together my own little step-by-step here.

Don't be haters, alligators. And the [Bible](https://wiki.archlinux.org) is amazeballs, don't get me worng.

## Table of Contents

[**Preparation**](https://github.com/fehawen/arch-guide#preparation)

1. [Creating a Live USB](https://github.com/fehawen/arch-guide#1-creating-a-live-usb)
	* 1.1. [Terminal](https://github.com/fehawen/arch-guide#11-terminal)
		* 1.1.1. [macOS](https://github.com/fehawen/arch-guide#111-macos)
		* 1.1.2. [Linux](https://github.com/fehawen/arch-guide#112-linux)
		* 1.1.3. [Windows](https://github.com/fehawen/arch-guide#113-windows)
	* 1.2. [GUI](https://github.com/fehawen/arch-guide#12-GUI)
2. [Booting into Arch](https://github.com/fehawen/arch-guide#2-booting-into-arch)
	* 2.1. [Apple](https://github.com/fehawen/arch-guide#21-apple)
	* 2.2. [Others](https://github.com/fehawen/arch-guide#22-others)
3. [UEFI or BIOS](https://github.com/fehawen/arch-guide#3-uefi-or-bios)

[**Installation**](https://github.com/fehawen/arch-guide#installation)

4. [Keymap](https://github.com/fehawen/arch-guide#4-keymap)
5. [Font Size](https://github.com/fehawen/arch-guide#5-font-size)
6. [Internet Connection](https://github.com/fehawen/arch-guide#6-internet-connection)
	* 6.1. [Ethernet](https://github.com/fehawen/arch-guide#61-ethernet)
	* 6.2. [Wi-Fi](https://github.com/fehawen/arch-guide#62-wi-fi)
		* 6.2.1 [wpa_supplicant](https://github.com/fehawen/arch-guide#621-wpa_supplicant)
		* 6.2.2 [wifi-menu](https://github.com/fehawen/arch-guide#622-wifimenu)
7. [Partitioning](https://github.com/fehawen/arch-guide#7-partitioning)
	* 7.1. [Notes](https://github.com/fehawen/arch-guide#71-notes)
	* 7.2. [Partitioning for GRUB](https://github.com/fehawen/arch-guide#72-partitioning-for-grub)
	* 7.3. [Partitioning for systemd](https://github.com/fehawen/arch-guide#73-partitioning-for-systemd)
8. [Install Base system](https://github.com/fehawen/arch-guide#8-install-base-system)
9. [Configure Installation](https://github.com/fehawen/arch-guide#9-configure-installation)
10. [Install Bootloader](https://github.com/fehawen/arch-guide#10-install-bootloader)
	* 10.1. [GRUB](https://github.com/fehawen/arch-guide#101-grub)
	* 10.2. [systemd](https://github.com/fehawen/arch-guide#102-systemd-boot)

## Known issues

The Dell DW1560 802.11ac adapter (based on the Broadcom BCM4352 chip) requires `broadcom-wl` or `broadcom-wl-dkms`, and also `linux-headers` (even though it's listed as an optional dependency).

See [Arch Wiki - Dell XPS 13 (9343)](https://wiki.archlinux.org/index.php/Dell_XPS_13_(9343)) and [Arch Wiki - Broadcom wireless](https://wiki.archlinux.org/index.php/Broadcom_wireless) for more information.

## Note on writing to file

When writing to file with `>`, the file's existing content is deleted and replaced with the command's output.

When writing to file with `>>`, the file's existing content is left untouched, and the command's output is appended to the end of the file.

# Preparation

## 1. Creating a Live USB

Grab a USB stick, download the [Arch ISO](https://www.archlinux.org/download/), and get crackin'.

### 1.1 Terminal

#### 1.1.1. macOS

Give your terminal the following to chew on, keeping in mind to replace **X** with the number that points to the disk that is your USB stick. And wahtever you do, don't get that number wrong, or you'll wipe out whichever disk you accidentally entered. Don't sweat it.

<pre>
$ diskutil list
$ diskutil unmountDisk /dev/disk<b>X</b>
$ sudo dd if=path/to/arch.iso of=/dev/rdisk"X" bs=1m
</pre>

#### 1.1.2. Linux

If you're on a Linux system, there are a number of options such as `unetbootin` and `ddrescue`, to name a few.

Have a look [here](https://wiki.archlinux.org/index.php/USB_flash_installation_media#In_GNU/Linux) for more information.

#### 1.1.3. Windows

If you're on Windows, have a look [here](https://wiki.archlinux.org/index.php/USB_flash_installation_media#In_Windows) for more information.

### 1.2. GUI

For a GUI way of doing it, regardless of whether you're on macOS, Linux or Windows, you can use something like [Etcher](https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_etcher), which does it all for you in a self-explanatory manner.

## 2. Booting into Arch

### 2.1. Apple

Reboot your computer and press **C** to boot directly from a second boot device – in this case your Live USB.

You can also press **Option** (i.e. the **alt** key) to enter the boot menu, from where you acan choose your installation media – again, your Live USB in this case.

### 2.2. Others

On other systems/machines, changing the boot order or boot device is done in the BIOS settings menu, which can be accessed by spamming something like `F2` or `F6` on startup.

You can also spam something like `F10`, which'll give you a menu form which you'll have to navigate to the BIOS settings/setup menu.

Exactly which key brings up said menus on startup varies, so a quick search on your specific machine might be necessary.

## UEFI

Check that our system uses `UEFI`, which it should.

If the command returns a list of files, we're good to go.

<pre>
$ ls /sys/firmware/efi/efivars
# or
$ efivar -l
</pre>

## Keymap

Make typing commands a bit easier by setting a temporary keymap (Swedish layout in my case) for the current session.

Permanent keymap will be set later on.

<pre>
$ loadkeys sv-latin1
</pre>

For other options, list available keymaps in `/usr/share/kbd/keymaps/`.

<pre>
$ ls /usr/share/kbd/keymaps/
</pre>

## 5. Font Size

The QHD or UHD screen resolution might make the font look incredibly small, which we can fix temporarily during the installation.

<pre>
# setfont fontname fontsize x lineheight
$ setfont sun12x24
</pre>

Or list avaiable fonts for options.

<pre>
$ fc-list
</pre>

## 6. Internet Connection

Depending on the situation, hardware, prerequisites and preference, set up a connection using either `ethernet` or `wi-fi`.

If you for some reason – adaptor issues, lack of ports, forgot to pay the internet bill, stuck in the woods, whatever – can't connect an ethernet cable or don't have access to wifi, tethering from your phone over USB might be an option.

Thethering should work fine, at least if it's an **Android** phone (or so I've heard). If it's an **iPhone** you're currently sporting however, then perhaps you ought to read [this](https://wiki.archlinux.org/index.php/IPhone_tethering).

### 6.1. Ethernet

Detect your network interface, called something like `enp0s25` or whatever.

<pre>
$ ip link
</pre>

Connect the interface to ze internetz using `dhcpcd`.

<pre>
$ dhcpcd <interface>
</pre>

Ping something (hey, it rhymes!) to verify your connection, like ping G****e 5 times.

<pre>
$ ping 5 8.8.8.8
</pre>

*Or perhaps the **-c** flag for count is required, I can never remember.*

<pre>
$ ping -c 5 8.8.8.8
</pre>

### 6.2. Wi-Fi

#### 6.2.1 wpa_supplicant

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

#### 6.2.2. wifi-menu

*To be written.*

## Partitioning

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

## EFI file system

Create `FAT32` file system for `EFI` partition.

<pre>
# EFI partition created first, so it's 1
$ mkfs.fat -F32 /dev/sda1
</pre>

## LUKS encryption

Encrypt Linux `LVM` partition with `LUKS`.

<pre>
# LVM partition created second, so it's 2
$ cryptsetup luksFormat /dev/sda2
</pre>

Open encrypted partition to enable installation.

<pre>
$ cryptsetup open --type luks /dev/sda2 <b>lvm</b>
</pre>

Check that it exists.

<pre>
$ ls /dev/mapper/lvm # should return: /dev/mapper/lvm
</pre>

Create physical volume on top of */dev/mapper/lvm*.

<pre>
$ pvcreate /dev/mapper/lvm
</pre>

Create volume group.

<pre>
$ vgcreate volume /dev/mapper/lvm # Replace 'volume' with preferred name
</pre>

Create logical volume for *swap*.

<pre>
# 12 GB (150% of 8 GB RAM) SWAP in the 'volume' group, named 'swap', which results in 'dev/mapper/volume-swap'
$ lvcreate -L12G volume -n swap
</pre>

Create logical volume for *root*.

<pre>
# 30 GB root volume in the 'volume' group, named 'root', which results in 'dev/mapper/volume-root'
$ lvcreate -L30G volume -n root
</pre>

Create logical volume for *home*.

<pre>
# Remaining space home volume in the 'volume' group, named 'home', which results in 'dev/mapper/volume-home'
$ lvcreate -l 100%FREE volume -n home
</pre>

## LVM device mapping

Make SWAP for *swap*.

<pre>
$ mkswap /dev/mapper/volume-swap
</pre>

Make EXT4 file system for *root*.

<pre>
$ mkfs.ext4 /dev/mapper/volume-root
</pre>

Make EXT4 file system for *home*.

<pre>
$ mkfs.ext4 /dev/mapper/volume-home
</pre>

## Mount Volumes

Mount *root* to */mnt*.

<pre>
$ mount /dev/mapper/volume-root /mnt
</pre>

Make */mnt/home* directory.

<pre>
$ mkdir /mnt/home
</pre>

Make *boot/mnt/boot* directory.

<pre>
$ mkdir /mnt/boot
</pre>

Mount *home* to */mnt/home*

<pre>
$ mount /dev/mapper/volume-home /mnt/home
</pre>

Mount the *EFI* boot partition to */mnt/boot*

<pre>
# Note that we're using our EFI /dev/sda1 partition here, as it's only our encrypted LVM partition that keeps holds the volumes 'root' and 'home'
$ mount /dev/sda1 /mnt/boot
</pre>

Acivate *swap*.

<pre>
$ swapon /dev/mapper/volume-swap
</pre>

## Install base system

Install the base system, including `base` and `base-devel`, along with whichever packages might be required at this point.

<pre>
$ pacstrap /mnt base base-devel network-manager nvim
</pre>

## Generate fstab

Take a peek to check that everything looks all right first.

<pre>
$ genfstab /mnt
</pre>

Output `fstab` to `/mnt/etc/fstab`, with `-U` flag to invoke use of UUIDs.

<pre>
$ genfstab -U /mnt >> /mnt/etc/fstab
</pre>

## Configure installation

Get *root* access to the system itself.

<pre>
$ arch-chroot /mnt
</pre>

Set timezone by symlinking the desired `zone` with `subzone` to `/etc/localtime`.

<pre>
# The -v flag is just for verbose output
$ ln -sv /usr/share/zoneinfo/<b>zone</b>/<b>zubsone</b> /etc/localtime
</pre>

*If needed, look through `/usr/share/zoneinfo` first for *zone* and *subzone* options.

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
# Uncomment preferred choice, e.g. Swedish for me
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
</pre>

Set permanent keymap for keyboard layout (we did this before, but temporarily), by editing `/etc/vconsole.conf`.

<pre>
$ nvim /etc/vconsole.conf
</pre>

Edit the necessary line with your preferred layout (again, Swedish for me).

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

Create a user account.

<pre>
$ useradd -m -g users -G wheel -s /bin/bash <b>username</b> && passwd <b>password</b>
</pre>

Edit `/etc/sudoers` to allow `sudo` commands without being asked for password all the time. And yes, this may or may not be good idea.

<pre>
$ nvim /etc/sudoers

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

Search for the variable `HOOKS` inside `/etc/mkinitcpio.conf` and rearrange the order so that `keyboard` is listead before `filesystems`, and adding `encrypt` and `lvm2` (also before `filtesystems`)

<pre>
...
HOOKS="... keyboard encrypt lvm2 filesystems ..."
...
</pre>

Next, regenerate our updated `mkinitcpio.conf`, which will regenrate our `initramfs` as well.

<pre>
$ mkinitcpio -p linux
</pre>

## Install bootloader

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
initrd /initramfs-linux.img
options cryptdevice=UUID=<b>XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX</b>:volume root=/dev/mapper/volume-root quiet rw

# Get the UUID from within <em>vim</em> or <em>nvim</em>.
:read ! blkid /dev/sda2

# NOTE: It's <em>/dev/sda2</em> due to that being our LUKS formatted LVM partition holding our encrypted <em>/root</em>, <em>/home</em> and <em>/swap</em> logical volumes, due to the <em>boot</em> partition being created first on <em>/dev/sda1</em>.
</pre>

Exit the *chroot* session.

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