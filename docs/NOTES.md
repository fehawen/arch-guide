<h1 align="center">
	<a href="https://github.com/fehawen/arch-guide/blob/master/docs/INSTALL.md">
		<img alt="xps installation" src="https://user-images.githubusercontent.com/36552788/59856089-7df2d100-9376-11e9-906a-cc3f8a6d6001.png" width="250">
	</a>
	<br>
	<br>
	Arch Linux on Dell XPS 13 (9343)
	</h1>

## Notes

### Arch Linux `base` package changes

<pre>
--- old-base.txt	2019-10-06 20:46:27.801837395 +0800
+++ new-base.txt	2019-10-06 20:48:52.316676476 +0800
@@ -1,11 +1,6 @@
 bash
 bzip2
 coreutils
-cryptsetup
-device-mapper
-dhcpcd
-diffutils
-e2fsprogs
 file
 filesystem
 findutils
@@ -15,36 +10,17 @@
 glibc
 grep
 gzip
-inetutils
 iproute2
 iputils
-jfsutils
-less
 licenses
 linux
-linux-firmware
-logrotate
-lvm2
-man-db
-man-pages
-mdadm
-nano
-netctl
 pacman
 pciutils
-perl
 procps-ng
 psmisc
-reiserfsprogs
-s-nail
 sed
 shadow
-sysfsutils
-systemd-sysvcompat
+systemd
 tar
-texinfo
-usbutils
 util-linux
-vi
-which
-xfsprogs
+xz
</pre>

### WiFi Management

Use `wifi-menu` only to create profile, thereafter use `netctl` to enable auto-connects.

Enable auto-roaming mode.

<pre>
$ sudo systemctl enable netctl-auto@wlp2s0.service
</pre>

Enable a profile for automatic selection.

<pre>
$ netctl-auto enable wlp2s0-NameOfProfile
</pre>

Connecting to profile manually.

<pre>
$ sudo netctl-auto switch-to wlp2s0-NameOfProfile
</pre>

List profiles.

<pre>
$ netctl-auto list
</pre>

### Issues

External keyboard with same physical layout does not adhere to applied keymap/layout.

This might help, but unverified.

<pre>
localectl set-keymap se
localectl set-x11-keymap se
</pre>

Might be helpful to see what the logs say.

<pre>
$ cat ~/.local/share/xorg/Xorg.0.log
</pre>

Connection to Docking station with DisplayLink for multiple screens etc.

Install AUR packages.

<pre>
https://aur.archlinux.org/evdi-git.git
https://aur.archlinux.org/displaylink.git
</pre>

Enable `displaylink.service`

<pre>
systemctl enable displaylink.service
</pre>

Configure `/usr/share/X11/xorg.conf.d/20-evdidevice.conf`

<pre>
Section "OutputClass"
        Identifier "DisplayLink"
        MatchDriver "evdi"
        Driver "modesetting"
        Option  "AccelMethod" "none"
EndSection
</pre>

Reboot.

After that, run:

<pre>
$ xrandr --listproviders

# Providers: number : 2
# Provider 0: id: 0x49 cap: 0xb, Source Output, Sink Output, Sink Offload crtcs: 2 outputs: 8 associated providers: 0 name:Intel
# Provider 1: id: 0x13c cap: 0x2, Sink Output crtcs: 1 outputs: 1 associated providers: 0 name:modesetting
</pre>

In the above output, we can see that provider 0 is the system's regular graphics provider (Intel), and provider 1 (modesetting) is the DisplayLink provider. To use the DisplayLink device, connect provider 1 to provider 0:

<pre>
$ xrandr --setprovideroutputsource 1 0
</pre>

For more details, read <a href="https://wiki.archlinux.org/index.php/DisplayLink">this</a>

And laos read <a href="https://savoisn.github.io/post/displaylink_on_arch/">this</a>
