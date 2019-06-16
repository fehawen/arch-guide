<h1 align="center">
  <a href="https://github.com/fehawen/arch-guide">
    <img alt="arch rice" src="https://user-images.githubusercontent.com/36552788/51067962-869ce980-1617-11e9-892b-53df540776b5.png" width="350">
  </a>
  <br>Arch Linux
	<br>Installation Guide & Rice Config
</h1>

# About

Yes, yes, I know. RTFM and all that crap. Some hardcore Linux users out there might frown upon the fact that I'm writing my own installation guide/notes (and sharing them with the rest of the world - OMG! Open Source!? ) instead of reading the [Bible](https://wiki.archlinux.org/index.php/installation_guide) from top to bottom, as if installing an OS is the only thing ever worth spending your time on doing.

But, I can't be arsed to memorize all the darn commands and steps and options and keep-in-minds and whatnots, and I don't fancy having a gazillion browser tabs open whenever I need to reinstall it, so I'll do what's forbidden and put together my own little cheatsheet here. Don't be haters, alligators.

# Install

## Table of Contents

[**Preparations**](https://github.com/fehawen/arch-guide#preparations)

1. [Installation Medium](https://github.com/fehawen/arch-guide#1-installation-medium)
2. [Booting into Arch](https://github.com/fehawen/arch-guide#2-booting-into-arch)
3. [Keymap](https://github.com/fehawen/arch-guide#3-keymap)
4. [Internet Connection](https://github.com/fehawen/arch-guide#4-internet-connection)
	* 4.1. [Ethernet](https://github.com/fehawen/arch-guide#41-ethernet)
	* 4.2. [Wi-Fi](https://github.com/fehawen/arch-guide#42-wi-fi)
		* 4.2.1 [wpa_supplicant](https://github.com/fehawen/arch-guide#421-wpa_supplicant)

[**Installation**](https://github.com/fehawen/arch-guide#installation)

5. [Notes on Partitioning](https://github.com/fehawen/arch-guide#installation)
6. [Generic (GRUB)](https://github.com/fehawen/arch-guide#6-generic-grub)
	* 6.1. [Partitioning](https://github.com/fehawen/arch-guide#61-partitioning)
	* 6.2. [Format & Mount](https://github.com/fehawen/arch-guide#62-format--mount)
	* 6.3. [Install Base system](https://github.com/fehawen/arch-guide#63-install-base-system)
	* 6.4. [Configure Installation](https://github.com/fehawen/arch-guide#64-configure-installation)
7. [MacBook - Notes on Broadcom 43xx Chipsets](https://github.com/fehawen/arch-guide#7-notes-on-macbook-broadcom-43xx-chipsets)
8. [MacBook 6,1 (EFI / systemd-boot)](https://github.com/fehawen/arch-guide#8-macbook-61)
9. [MacBook Pro 11,1 (EFI / systemd boot)](https://github.com/fehawen/arch-guide#9-macbook-pro-111)


## Preparations

### 1. Installation Medium

Grab a USB stick, download the [Arch ISO](https://www.archlinux.org/download/), and get crackin'.

If you're on **macOS**, give your terminal the following to chew on, keeping in mind to replace **X** with the number that points to the disk that is your USB stick. And wahtever you do, don't get that number wrong, or you'll wipe out whichever disk you accidentally entered. Don't sweat it.

```
$ diskutil list
$ diskutil unmountDisk /dev/disk<X>
$ sudo dd if=path/to/arch.iso of=/dev/rdisk<X> bs=1m
```

If you're not on macOS, have a look [here](https://wiki.archlinux.org/index.php/USB_flash_installation_media) on how to do it.

For a GUI way of doing it, you can use something like [Etcher](https://wiki.archlinux.org/index.php/USB_flash_installation_media#Using_etcher), which make be easier for you in some sense.

### 2. Booting into Arch

Again, if you're on **macOS** (or more specifically an Apple machine), reboot your computer and hold **C** to boot directly from a second boot device – in this case your Live USB, or hold **Option** to enter the boot menu from where you'll choose your installation media – again, your Live USB in this case.

On other systems/machines, changing the boot order or boot device is done in the BIOS settings menu, which can be accessed by spamming something like `F2` or `F6` on startup – or with `F10` from where you'll have to navigate the BIOS settings/setup menu. Exactly which key brings up said menu on startup varies, but a quick search on your specific machine should give you the answer.

### 3. Keymap

Make typing commands a bit easier by setting a temporary keymap (Swedish layout) for the current session.

```bash
$ loadkeys sv-latin1
```
Permanent keymap/keyboard layout will be set later on. Use whenever needed until then.

### 4. Internet Connection

Depending on the situation, hardware, prerequisites etc, set up a connection using either `ethernet` or `wi-fi`.

Do you have one of those fancy new laptops with something ridiculous as perhaps a single USB-C port (Apple...), which requires you buy a buttload of (unreasonably expensive) adaptors to accomodate your connections? Then tethering from your phone over USB should work fine, at least if it's an **Android** phone (or so I've heard), and as long as you have the appropriate adaptor of course. If it's an **iPhone** you're currently sporting, then perhaps you ought to read [this](https://wiki.archlinux.org/index.php/IPhone_tethering).

Otherwise, don't bother, just connect via wi-fi and hope it's working.

#### 4.1. Ethernet

Detect your network interface, called something like `enp0s25` or whatever.

```
$ ip link
```

Connect the interface to ze internets using `dhcpcd`.

```
$ dhcpcd <interface>
```

Ping something (hey, it rhymes!) to verify your connection, like ping Google 5 times.

```
$ ping 5 8.8.8.8
```
*Or perhaps the -c flag for count is required, I can never remember.*

```
$ ping -c 5 8.8.8.8
```

#### 4.2. Wi-Fi

##### 4.2.1 wpa_supplicant

Check the wireless card status.

```
$ rfkill list
```

If it's blocked, try to unblock it.

```
$ rfkill unblock wifi
```

Find name of wireless interface, usually something like `wlp2s0`, `wlp3s0b1` or whatevs.

```
$ iwconfig
```

Scan for wireless networks, unless you already know yours.

```
$ iwlist <interface> scan
```
Or use.
```
$ iwlist <interface> scan | grep ESSID
```

Create a `wpa_supplicant.conf` file with `wpa_passphrase`.

```
$ wpa_passphrase "<ESSID>" "<passphrase>" | tee /etc/wpa_supplicant.conf
```

Connect to the wireless access point.

*Running in foreground.*

```
$ wpa_supplicant -c /etc/wpa_supplicant.conf
```

*Running in background.*

```
$ wpa_supplicant -B -c /etc/wpa_supplicant.conf
```

Check wireless interface status again, to verify.

```
$ iwconfig
```

Obtain private IP address from DHCP server.

```
$ dhclient <interface>
```

Show obtained private IP address.

```
$ ifconfig <interface>
```

Use `-r` flag to release private IP address, when/if needed.

```
$ dhclient <interface> -r
```

##### wifimienu

## Installation

### 5. Notes on Partitioning

For myself, to remember.

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

### 6. Generic (GRUB)

#### 6.1. Partitioning

List all drives, like `/dev/sda1` and `/dev/sda2` etc.

```
$ fdisk -l
```

Note that e.g. `/dev/sda<number>` are the partitions, i.e. `/dev/sda` is the main hard drive's alias which we'll use in the next step.

Proceed using a partition manager, which is easier for a rookie like me.

```
$ cfdisk /dev/sda
```

Seeing as how Arch will be the only OS, we'll proceed and delete all partitions.

Next, create a `SWAP` partition, doing it first to simply use the remaining free space for the one and only `/root` partition.

In `cfdisk`, choose `New -> Primary`.

```
Size (in MB): <size>
```

Size should be either same or half as RAM, who cares. Set it to either `2048` or `4096`, that'll do. *EDIT:* Actually, after thoughtful consideration (yeah, right...) and mainly some good ol' research, SWAP should be larger than RAM in order for hibernation etc to work properly, so keep it at either `RAM + 2GB`, or `RAM * 150%` to be sure, unless you have like 32GB RAM and would end up with a ridiculously large 48GB SWAP drive. But for the SWAP to be 6GB, or `6144` with 4GB RAM sound somewhat reasonable to me.

```
Beginning or End of Free Space?
```

Set this to `END`. Don't ask, just do it.

Next, create the `/root` partition in `cfdisk` with `New -> Primary` and give it the remaining free space.

```
Size (in MB): <remaining>
```

Remember to mark this partition as `Bootable`, because this is where the bootloader will be installed.

Next, select the `SWAP` and set its type to `82` in `Options -> Type`.

```
Enter filesystem type: 82
```

82 indicates that it's the SWAP drive.

Next, simply write out the changes and accept to execute the partitioning. Now would also be a good time to memorize the drive names, so as to not mix up the `SWAP` and `/root`. That'd suck.

We made the `SWAP` partition first, so that ought to be `/dev/sda1`, which'd make our `/root` partition come second at `/dev/sda2`. Or, you know, `/dev/disk1` and `/dev/disk2` or whatever.

#### 6.2. Format & Mount

First, let's format our `/root` partition to `ext4`.

```
$ mkfs.ext4 /dev/sda2
```

Next, let's mount our `/root` to `/mnt`

```
$ mount /dev/sda2 /mnt
```

Then, let's make a swapfile on our `SWAP` partition.

```
$ mkswap /dev/sda1
```

Last, let's get our swagger on by swapping on the `SWAP`.

```
$ swapon /dev/sda1
```

#### 6.3. Install Base System

Install the `base` system, as well as `base-devel` for dependency coverage.

```
$ pacstrap /mnt base base-devel
```

#### 6.4. Configure Installation

Get *root* access to the system itself.

```
$ arch-chroot /mnt
```

Set locale by uncommenting preferred locale(s) in `/etc/locale.gen`.

```
$ nano /etc/locale.gen
```

```
# en_SG ISO-8859-1
en_US.UTF-8 UTF-8
# en_US ISO-8859-1
```

Generate locale file.

```
$ locale-gen
```

Set selected locale upon boot by writing it to `locale.conf`, with `LANG=en_US.UTF-8` as example.

```
$ echo LANG=en_US.UTF-8 > /etc/locale.conf
```

Set timezone by symlinking it to `/etc/localtime`.*

```
$ ln -s /usr/share/zoneinfo/<zone>/<zubsone> /etc/localtime
```

*If needed, look through `/usr/share/zoneinfo` first for *zone* and *subzone* options.

```
$ cd /usr/share/zoneinfo
```

Set hardware clock from current system time.

```
$ hwclock --systohc --utc
```

Set host name.

```
$ echo <host_name> > /etc/hostname
```

*Note that when writing to file with `>`, you delete the file's content and replace it with whatever you `echo` to that file.*

*When writing to file with `>>`, you simply append whatever you `echo` to the end of that file, leaving already existing content of that file intact. Just a reminder for myself.*

Set new *root* password.

```
$ passwd
```

Create user account.

```
$ useradd -m -g users -G wheel -s /bin/bash <username> && passwd <password>
```

Open `/etc/sudoers` with e.g. `nano` or whatever.

```
$ nano /etc/sudoers
```

Uncomment this line, somewhere at the end of that file, to allow the use of `sudo` commands without being asked for password all the time.

```
$ %wheel ALL=(ALL) NOPASSWD: ALL
```

Lock the *root* account via `passwd`.

```
$ passwd -l root
```

To unlock it, whenever needed, use the `-u` flag.

```
$ sudo passwd -u root
```

Install GRUB bootloader.

```
$ pacman -S grub-bios
```

Install GRUB on your hard drive (not the partition itself).

```
$ grub-install /dev/sda
```

Generate *init* file for GRUB to load Linux.

```
$ mkinitcpio -p linux
```

Generate GRUB *config* file.

```
$ grub-mkconfig -o /boot/grub/grub.cfg
```

Exit current *chroot* session, as we're done here for now.

```
$ exit
```

Generate *fstab* file based on hard drive, for information about system partitions.

```
$ genfstab -U -p /mnt >> /mnt/etc/fstab
```

Unmount *root* device.

```
$ umount /mnt
```

Reboot into your new installation, making sure to remove the installation media first.

```
$ reboot
```

### 7. Notes on MacBook Broadcom 43xx Chipsets

As also mentioned further down, the MacBooks with `Broadcom 43xx` chipsets will most likely require a [proprietary driver](https://wiki.archlinux.org/index.php/Broadcom_wireless) in order to be supported. If it's working out-of-the-box (i.e. wireless available from the installer), you may not have to worry about — it might be supported by the open source `brcmfac`, included by default in the kernel. Have a look at the [brcm80211](https://wiki.archlinux.org/index.php/Broadcom_wireless#brcm80211) section for details.

### 8. MacBook 6,1

My ragged old **MacBook 6,1 (Polycarbonate Unibody, 13-inch, Late 2009, Nvidia GeForce 9400M GPU)** features an `AirPort Extreme (0x14E4, 0x93)` wireless card with `Broadcom BCM43xx 1.0 (5.106.98.102.30)` firmware version.

For firmware related issues and updates, have a look at the [b43-firmware](https://aur.archlinux.org/packages/b43-firmware/) package.

Some relevant info might (perhaps) also be found in the [MacBook5,2](https://wiki.archlinux.org/index.php/MacBook5,2_(early-mid_2009)) Arch wiki article.

*[Installation notes to go here...]*

### 9. MacBook Pro 11,1

My still-in-good-shape workhorse **MacBook Pro 11,1 (Retina, 13-inch, Late 2013, Intel GPU)** fetaures an `AirPort Extreme (0x14E4, 0x112)` wireless card with `Broadcom BCM43xx 1.0 (7.77.37.31.1a9)` firmware version.

The [MacBookPro11,x](https://wiki.archlinux.org/index.php/MacBookPro11,x) Arch wiki article includes most, if not all, of what's worth knowing about issues etc.

*[Installation notes to go here...]*
