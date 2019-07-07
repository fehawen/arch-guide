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
$ pacman -S xorg-server xorg-xinit xorg-xrandr xorg-xsetroot
</pre>

Install window manager, terminal, web and file browser, etc.

<pre>
$ pacman -S i3-gaps i3blocks kitty dmenu firefox ranger git xf86-input-libinput
</pre>

Edit touchpad configuration.

<pre>
$ sudo nvim /usr/share/X11/xorg.conf.d/40-libinput.conf
</pre>

<pre>
Section "InputClass"
        Identifier "libinput touchpad catchall"
        MatchIsTouchpad "on"
        MatchDevicePath "/dev/input/event*"
        Option "Tapping" "true"
        Option "TappingDrag" "true"
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

Install python pillow library to enable image preview with Ranger in Kitty terminal.

<pre>
$ sudo pacman -S python-pillow
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
