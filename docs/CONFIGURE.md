<h1 align="center">
	<a href="https://github.com/fehawen/arch-guide/blob/master/docs/INSTALL.md">
		<img alt="xps installation" src="https://user-images.githubusercontent.com/36552788/59856089-7df2d100-9376-11e9-906a-cc3f8a6d6001.png" width="250">
	</a>
	<br>
	<br>
	Arch Linux on Dell XPS 13 (9343)
	</h1>

## Configuration

Install X.

<pre>
$ sudo pacman -S xorg-server xorg-xinit xorg-xrandr xorg-xsetroot
</pre>

Install additional packages.

<pre>
$ sudo pacman -S i3-gaps i3lock conky kitty dmenu firefox surf gtop ranger git xf86-input-libinput pacman-contrib scrot newsboat zip unzip
</pre>

Change MAKEFLAGS to use all threads in for faster builds, e.g. AUR packages

<pre>
$ sudoedit /etc/makepkg.conf
</pre>

<pre>
$ MAKEFLAGS="-j${nproc}"
</pre>

Edit touchpad configuration.

<pre>
$ sudoedit /usr/share/X11/xorg.conf.d/40-libinput.conf
</pre>

<pre>
Section "InputClass"
        Identifier "libinput touchpad catchall"
        MatchIsTouchpad "on"
        MatchDevicePath "/dev/input/event*"
        Option "Tapping" "true"
        Option "TappingDrag" "false"
        Option "NaturalScrolling" "true"
        Option "AccelProfile" "linear"
        Option "AccelSpeed" "0.25"
        Option "DisableWhileTyping" "false"
        Driver "libinput"
EndSection
</pre>

Install drivers for Intel graphics.

<pre>
$ sudo pacman -S xf86-video-intel mesa
</pre>

Install packages to enable image preview for Ranger/Kitty.

<pre>
$ sudo pacman -S python-pillow imagemagick
</pre>

Install audio packages.

<pre>
$ sudo pacman -S alsa-utils alsa-lib pulseaudio pamixer
</pre>

Install developers tools and other useful packages.

<pre>
$ sudo pacman -S npm docker docker-compose zathura poppler zathura-pdf-poppler
</pre>

Start and enable Docker.

<pre>
$ systemctl start docker
</pre>

<pre>
$ systemctl enable docker
</pre>

Clone dotfiles.

<pre>
$ git clone https://github.com/fehawen/dotfiles.git ~/Dotfiles
</pre>

Run the setup.

<pre>
$ cd ~/Dotfiles && make
</pre>

Done.
