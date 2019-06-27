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
$ pacman -S i3-gaps i3status kitty dmenu firefox xf86-input-libinput
</pre>

Install additional packages.

<pre>
$ pacman -S git
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
