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
$ pacman -S xorg-server xorg-xinit xorg-xrandr
</pre>

Install window manager, terminal, etc.

<pre>
$ pacman -S i3-gaps i3status kitty dmenu firefox git fira-code xf86-input-libinput
</pre>

Fix scaling for screen, additionally to X config.

<pre>
$ sudo touch /etc/profile.d/hidpi.sh
<pre>

<pre>
$ sudo nvim /etc/profile.d/hidpi.sh
<pre>

<pre>
	export GDK_SCALE=2
	export GDK_DPI_SCALE=0.5
	export QT_AUTO_SCREEN_SCALE_FACTOR=0
	export QT_SCREEN_SCALE_FACTORS=2
	export QT_QPA_PLATFORMTHEME=qt5ct
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
