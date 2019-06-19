<h1 align="center">
	<a href="https://github.com/fehawen/arch-guide">
		<img alt="arch rice" src="https://user-images.githubusercontent.com/36552788/59663744-f6f3fc00-91af-11e9-9386-bd18df3ae57a.png" width="600">
	</a>
	<br>Arch Linux
	<br>Installation Guide
</h1>

# About

Yes, yes, I know. RTFM and all that... Some folks out there might frown upon the fact that I'm writing my own installation guide/notes (and sharing them with the rest of the world - OMG! Open Source!?) instead of reading the [Bible](https://wiki.archlinux.org) from cover to cover, as if installing an OS is the only thing ever worth spending time on.

But, I can't be arsed to memorize all the darn commands and steps and options and keep-in-minds and whatnots, and I don't fancy having a gazillion browser tabs open whenever I need to reinstall it, so I'll do what's forbidden and put together my own little cheatsheet here.

Don't be haters, alligators.

# Table of Contents

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
		* 6.2.2 [wifimenu](https://github.com/fehawen/arch-guide#622-wifimenu)
7. [Partitioning](https://github.com/fehawen/arch-guide#7-partitioning)
	* 7.1. [Notes](https://github.com/fehawen/arch-guide#71-notes)
	* 7.2. [Partitioning for GRUB](https://github.com/fehawen/arch-guide#72-partitioning-for-grub)
	* 7.3. [Partitioning for systemd](https://github.com/fehawen/arch-guide#73-partitioning-for-systemd)
8. [Install Base system](https://github.com/fehawen/arch-guide#8-install-base-system)
9. [Configure Installation](https://github.com/fehawen/arch-guide#9-configure-installation)
10. [Install Bootloader](https://github.com/fehawen/arch-guide#10-install-bootloader)
	* 10.1. [GRUB](https://github.com/fehawen/arch-guide#101-grub)
	* 10.2. [systemd](https://github.com/fehawen/arch-guide#102-systemd-boot)

# Preparation

## 1. Creating a Live USB

Grab a USB stick, download the [Arch ISO](https://www.archlinux.org/download/), and get crackin'.

### 1.1 Terminal

#### 1.1.1. macOS

Give your terminal the following to chew on, keeping in mind to replace **<X>** with the number that points to the disk that is your USB stick. And wahtever you do, don't get that number wrong, or you'll wipe out whichever disk you accidentally entered. Don't sweat it.

```bash
$ diskutil list
$ diskutil unmountDisk /dev/disk<X>
$ sudo dd if=path/to/arch.iso of=/dev/rdisk<X> bs=1m
```

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

## 3. UEFI or BIOS

Once you have your Live USB with Arch Linux and are able to successfully boot from it, you'll have to check if you need to use `UEFI` or `BIOS` before setting up the system's boot process.

```bash
$ ls /sys/firmware/efi/efivars
```

If the command returns a list of files, your system uses **UEFI**.

Although you might still be able to use BIOS in what's called `Legacy` mode – even if your system uses UEFI – we won't go into any details about that here, and for the sake of simplicity just stick to the rule that if your system uses UEFI, you setup your Arch installation using UEFI.

If the command does not return a list of files however, and instead returns something like `ls: cannot access '/sys/firmware/efi/efivars': No such file or directory`, then you know for certain that your system *does not* use UEFI, meaning you'll need to use `BIOS`. Sure, there might be certain options and ways to solve EFI compatibility should you wish to do so, but let's again stick to the rule that if your system supports UEFI, you use UEFI; if it doesn't support UEFI, you use BIOS.

*On a side note*, just for the sake of educating myself a bit here, as an alternative to using `ls /sys/firmware/efi/efivars` you can instead run the following command, which'll work just as well.

```bash
$ efivar -l
```

If this command returns something like `efivar: error listing variables: Function not implemented`, then your system uses **BIOS**. Otherwise, it'll return the same list of files as the `ls /sys/...` command would've done, meaning your system uses **UEFI**.

# Installation

## 4. Keymap

Make typing commands a bit easier by setting a temporary keymap (Swedish layout in my case) for the current session.

```bash
$ loadkeys sv-latin1
```

For other options, list available keymaps (I'm pretty sure they're in */usr/share/kbd/keymaps/*, but don't take my word for it).

```bash
$ ls /usr/share/kbd/keymaps/
```

Permanent keymap/keyboard layout will be set later on.

## 5. Font Size

If your on e.g. a HiDPI/Retina/QHD/UHD or whatever screen with insane resolution, the font size might look retardedly tiny, making you squint like a madman.

Change font size, for now, until you get everything set up and tweak it later on.

```bash
$ setfont sun12x24
```

Or replace `sun12x24` with whatever font or size you prefer, listing avaiable fonts for options.

```bash
$ fc-list
```

## 6. Internet Connection

Depending on the situation, hardware, prerequisites and preference, set up a connection using either `ethernet` or `wi-fi`.

If you for some reason – adaptor issues, lack of ports, forgot to pay the internet bill, stuck in the woods, whatever – can't connect an ethernet cable or don't have access to wifi, tethering from your phone over USB might be an option.

Thethering should work fine, at least if it's an **Android** phone (or so I've heard). If it's an **iPhone** you're currently sporting however, then perhaps you ought to read [this](https://wiki.archlinux.org/index.php/IPhone_tethering).

### 6.1. Ethernet

Detect your network interface, called something like `enp0s25` or whatever.

```bash
$ ip link
```

Connect the interface to ze internetz using `dhcpcd`.

```bash
$ dhcpcd <interface>
```

Ping something (hey, it rhymes!) to verify your connection, like ping G****e 5 times.

```bash
$ ping 5 8.8.8.8
```
*Or perhaps the **-c** flag for count is required, I can never remember.*

```bash
$ ping -c 5 8.8.8.8
```

### 6.2. Wi-Fi

#### 6.2.1 wpa_supplicant

Check the wireless card status.

```bash
$ rfkill list
```

If it's blocked, try to unblock it.

```bash
$ rfkill unblock wifi
```

Find name of wireless interface, usually something like `wlp2s0`, `wlp3s0b1` or whatevs.

```bash
$ iwconfig
```

Scan for wireless networks, unless you already know yours.

```bash
$ iwlist <interface> scan
```

*Or use.*

```bash
$ iwlist <interface> scan | grep ESSID
```

Create a `wpa_supplicant.conf` file with `wpa_passphrase`.

```bash
$ wpa_passphrase "<ESSID>" "<passphrase>" | tee /etc/wpa_supplicant.conf
```

Connect to the wireless access point.

*Running in foreground.*

```bash
$ wpa_supplicant -c /etc/wpa_supplicant.conf
```

*Running in background.*

```bash
$ wpa_supplicant -B -c /etc/wpa_supplicant.conf
```

Check wireless interface status again, to verify.

```bash
$ iwconfig
```

Obtain private IP address from DHCP server.

```bash
$ dhclient <interface>
```

Show obtained private IP address.

```bash
$ ifconfig <interface>
```

Use `-r` flag to release private IP address, when/if needed.

```bash
$ dhclient <interface> -r
```

#### 6.2.2. wifimenu

*To be written.*

## 7. Partioning

### 7.1. Notes

Some notes on partitioning, for myself to remember.

* **GUID Partioning Table (GPT)**

	* Can be used with either **BIOS** or **UEFI**.

* **Master Boot Record (MBR)**

	* Can be used only with **BIOS**, unless perhaps placed in *Legacy* mode.

* **Partitioning Tools**

	* **parted**

		* Supports *GPT* and *MBR*.

	* **fdisk, cfdisk, sfdisk**

		* Supports *GPT* and *MBR*.

	* **gdisk, cgdisk, sgdisk**

		* Supports *GPT* only.

### 7.2. Partitioning for GRUB

List all drives, like `/dev/sda1` and `/dev/sda2` etc.

```bash
$ fdisk -l
```

Note that e.g. `/dev/sda<number>` are the partitions, i.e. `/dev/sda` is the main hard drive's alias which we'll use in the next step and `<number>` is the number suffix to identify each partition.

Next, we'll proceed using the `cfdisk` partition manager.

```bash
$ cfdisk /dev/sda
```

Seeing as how Arch will be the only OS, we'll proceed and delete all partitions.

Next, create a `SWAP` partition, doing it first to simply use the remaining free space for the one and only `/root` partition.

In `cfdisk`, choose `New -> Primary`.

```bash
Size (in MB): <size>
```

The appropriate/necessary size of the SWAP partition is a neverending discussion – some say half as RAM, some say same as RAM, some say 150% of RAM, and a selected few paranoid androids even say twice as RAM. Me, I'll trust the argument that SWAP should be larger than RAM in order for hibernation, etc, to work properly, so I'll keep it at either `RAM + 2GB`, or `RAM * 150%` to be sure, unless I have like 32GB RAM and would end up with a ridiculously large 34GB or 48GB SWAP drive, in which case I would reconsider its size or whether I'd even need it. But for the SWAP to be 12GB, i.e. `12288 MB`, with 8GB RAM sounds somewhat reasonable to me.

```bash
Beginning or End of Free Space?
```

Set this to `END`. Don't ask, just do it.

Next, create the `/root` partition in `cfdisk` with `New -> Primary` and give it the remaining free space.

```bash
Size (in MB): <remaining>
```

Remember to mark this partition as `Bootable`, because this is where the bootloader will be installed.

Next, select the `SWAP` and set its type to `82` in `Options -> Type`.

```bash
Enter filesystem type: 82
```

82 indicates that it's the SWAP drive.

Next, simply write out the changes and accept to execute the partitioning. Now would also be a good time to memorize the drive names, so as to not mix up the `SWAP` and `/root`. That'd suck.

We made the `SWAP` partition first, so that ought to be `/dev/sda1`, which'd make our `/root` partition come second at `/dev/sda2`. Or, you know, `/dev/disk1` and `/dev/disk2` or whatever.

Next, we'll format our `/root` partition to `ext4`.

```bash
$ mkfs.ext4 /dev/sda2
```

Next, let's mount our `/root` to `/mnt`

```bash
$ mount /dev/sda2 /mnt
```

Then, let's make a swapfile on our `SWAP` partition.

```bash
$ mkswap /dev/sda1
```

Last, let's get our swagger on by swapping on the `SWAP`.

```bash
$ swapon /dev/sda1
```

### 7.3. Partitioning for systemd

*To be written.*

## 8. Install Base System

Install the `base` system, as well as `base-devel` for dependency coverage.

You can add any additional packages at this as well, such as e.g. `nvim` and whatnot, but I'll leave that up to you. As long as you install the aforementioned `base` and `base-devel`, you're good for now.

```bash
$ pacstrap /mnt base base-devel
```

## 9. Configure Installation

Get *root* access to the system itself.

```bash
$ arch-chroot /mnt
```

Set locale by uncommenting preferred locale in `/etc/locale.gen`.

```bash
$ nano /etc/locale.gen
```

Here, you'll most likely want `UTF-8`, unless for some reason you require ISO.

```bash
# en_US.UTF-8 UTF-8
# en_US ISO-8859-1
...
sv_SE.UTF-8 UTF-8 # Uncomment preferred choice, e.g. Swedish for me
# sv_SE ISO-8859-1
```

Generate locale file.

```bash
$ locale-gen
```

Set selected locale upon boot by writing it to `locale.conf`, with `LANG=sv_SE.UTF-8` as example.

```bash
$ echo LANG=sv_SE.UTF-8 > /etc/locale.conf
```

Set permanent keymap, i.e. keyboard layout (we did this before, but temporarily), by editing */etc/vconsole.conf*.

```bash
$ nano /etc/vconsole.conf
```

*Edit the necessary line with your preferred layout (again, Swedish for me).*

```bash
$ KEYMAP=sv-latin1
```

Set timezone by symlinking the desired *zone* with *subzone* to `/etc/localtime`.

```bash
# The -v flag is just for verbose output
$ ln -sv /usr/share/zoneinfo/<zone>/<zubsone> /etc/localtime
```

*If needed, look through `/usr/share/zoneinfo` first for *zone* and *subzone* options.

```bash
$ cd /usr/share/zoneinfo && ls
# or
$ ls /usr/share/zoneinfo
```

Set hardware clock from current system time.

```bash
$ hwclock --systohc --utc
```

Set host name, replacing `<host_name>` with your preferred name.

```bash
$ echo <host_name> > /etc/hostname
```

*Note that when writing to file with `>`, you delete the file's content and replace it with whatever you `echo` to that file.*

*When writing to file with `>>`, you simply append whatever you `echo` to the end of that file, leaving already existing content of that file intact. Just a reminder for myself.*

Set new *root* password.

```bash
$ passwd
```

Create user account, replacing `<username>` and `<password>`... You know the drill by now.

```bash
$ useradd -m -g users -G wheel -s /bin/bash <username> && passwd <password>
```

Open `/etc/sudoers` with e.g. `nano` or whatever.

```bash
$ nano /etc/sudoers
```

Uncomment this line, somewhere at the end of that file, to allow the use of `sudo` commands without being asked for password all the time. And, you know, do it **at your own risk**, as you probably guessed.

```bash
$ %wheel ALL=(ALL) NOPASSWD: ALL
```

Next, lock the *root* account via `passwd`.

```bash
$ passwd -l root
```

To unlock it, whenever needed, use the `-u` flag.

```bash
$ sudo passwd -u root
```

## 10. Install Bootloader

### 10.1. GRUB

Only do this if you followed the [Partitioning for GRUB]() step.

If you followed the [Partitioning for systemd]() step, the skip this section and proceed to the [systemd]() section.

```bash
$ pacman -S grub-bios
```

Install GRUB on your hard drive (not the partition itself).

```bash
$ grub-install /dev/sda
```

Generate *init* file for GRUB to load Linux.

```bash
$ mkinitcpio -p linux
```

Generate GRUB *config* file.

```bash
$ grub-mkconfig -o /boot/grub/grub.cfg
```

Exit current *chroot* session, as we're done here for now.

```bash
$ exit
```

Generate *fstab* file based on hard drive, for information about system partitions.

```bash
$ genfstab -U -p /mnt >> /mnt/etc/fstab
```

Unmount *root* device.

```bash
$ umount /mnt
```

Reboot into your new installation, making sure to remove the installation media first.

```bash
$ reboot
```

### 10.2. systemd-boot

*To be written.*

# Dell XPS 13 (9343)

## ? WiFi

The Dell DW1560 802.11ac adapter (based on the Broadcom BCM4352 chip) requires `broadcom-wl` or `broadcom-wl-dkms`, and also `linux-headers` (even though it's listed as an optional dependency).

See [Arch Wiki - Dell XPS 13 (9343)](https://wiki.archlinux.org/index.php/Dell_XPS_13_(9343)) and [Arch Wiki - Broadcom wireless](https://wiki.archlinux.org/index.php/Broadcom_wireless) for more information.

# UEFI and systemd-boot with LVM and LUKS

## Partition

Use gdisk for partitioning.

```bash
$ gdisk /dev/sda
# or, you know, might be something like
$ gdisk /dev/nvme0n1
```

Create an empty GPT partition table.

```bash
$ o
```

Create a new EFI partition (512 MB).

```bash
$ n

# Partition number (...):
# First sector (...):
Last sector (...): +512MiB

Hex code or GUID (...): ef00
```

Create a new Linux LVM partition (REMAINING SPACE).

```bash
$ n

# Partition number (...):
# First sector (...):
#Last sector (...):

Hex code or GUID (...): 8e00
```

Write changes, i.e. execute partitioning.

```bash
$ w
```

## EFI File System

Create FAT32 file system for EFI partition.

```bash
$ mkfs.fat -F32 /dev/sda1 # EFI partition created first, so it's 1
```

## LUKS Encryption

Encrypt Linux LVM partition with LUKS.

```bash
$ cryptsetup luksFormat /dev/sda2 # LVM partition created second, so it's 2
```

Open encrypted partition to enable installation.

```bash
$ cryptsetup open --type luks /dev/sda2 lvm # Replace 'lvm' with preferred name
```

Check that it exists.

```bash
$ ls /dev/mapper/lvm # should return: /dev/mapper/lvm
```

Create physical volume on top of **/dev/mapper/lvm**.

```bash
$ pvcreate /dev/mapper/lvm
```

Create volume group.

```bash
$ vgcreate volume /dev/mapper/lvm # Replace 'volume' with preferred name
```

Create logical volume for **swap**.

```bash
# 12 GB (150% of 8 GB RAM) SWAP in the 'volume' group, named 'swap', which results in 'dev/mapper/volume-swap'
$ lvcreate -L12G volume -n swap
```

Create logical volume for **root**.

```bash
# 30 GB root volume in the 'volume' group, named 'root', which results in 'dev/mapper/volume-root'
$ lvcreate -L30G volume -n root
```

Create logical volume for **home**.

```bash
# Remaining space home volume in the 'volume' group, named 'home', which results in 'dev/mapper/volume-home'
$ lvcreate -l 100%FREE volume -n home
```

## LVM File System

Make SWAP for **swap**.

```bash
$ mkswap /dev/mapper/volume-swap
```

Make EXT4 file system for **root**.

```bash
$ mkfs.ext4 /dev/mapper/volume-root
```

Make EXT4 file system for **home**.

```bash
$ mkfs.ext4 /dev/mapper/volume-home
```

## Mount Volumes

Mount **root** to **/mnt**.

```bash
$ mount /dev/mapper/volume-root /mnt
```

Make **/mnt/home** directory.

```bash
$ mkdir /mnt/home
```

Make **boot/mnt/boot** directory.

```bash
$ mkdir /mnt/boot
```

Mount **home** to **/mnt/home**

```bash
$ mount /dev/mapper/volume-home /mnt/home
```

Mount the **EFI** boot partition to **/mnt/boot**

```bash
# Note that we're using our EFI /dev/sda1 partition here, as it's only our encrypted LVM partition that keeps holds the volumes 'root' and 'home'
$ mount /dev/sda1 /mnt/boot
```

Acivate **swap**.

```bash
$ swapon /dev/mapper/volume-swap
```