<h1 align="center">
	<a href="https://github.com/fehawen/arch-guide/blob/master/docs/INSTALL.md">
		<img alt="xps installation" src="https://user-images.githubusercontent.com/36552788/59856089-7df2d100-9376-11e9-906a-cc3f8a6d6001.png" width="250">
	</a>
	<br>
	<br>
	Arch Linux on Dell XPS 13 (9343)
	</h1>

## Notes

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

Or this, also unverified.

<pre>
# /etc/X11/xorg.conf.d/00-keyboard.conf

Section "InputClass"
        Identifier "system-keyboard"
        MatchIsKeyboard "on"
        Option "XkbLayout" "cz,us"
        Option "XkbModel" "pc104"
        Option "XkbVariant" ",dvorak"
        Option "XkbOptions" "grp:alt_shift_toggle"
EndSection
</pre>

Might be helpful to see what the logs say.

<pre>
$ cat ~/.local/share/xorg/Xorg.0.log
</pre>
