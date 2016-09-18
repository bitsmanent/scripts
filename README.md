scripts
=======
Useful shell scripts.

index
-----
* [iwpick](#iwpick) - Connects to a network.
* [scrapthumb](#scrapthumb) - Get random images from Tumblr.
* [acpi](#acpi) - Simple power management.
* [dwmstatus](#dwmstatus) - Output useful informations suitable for a status bar.
* [spawncgi](#spawncgi) - Spawn the CGI daemon.
* [pronunciask](#pronunciask) - Get pronunciations from forvo.com.
* [pkgsetup](#pkgsetup) - Easy pkgsrc (and -wip) for non-NetBSD systems.
* [whexsafe](#whexsafe) - Web safe color list in hex and RGB.
* [swss](#swss) - Start/stop a group of services.
* [droid](#droid) - Sane Android development.
* [browse](#browse) - Little dmenu-based file browser.
* [diffmon](#diffmon) - Diff-based file monitor.
* [sober](#sober) - Simple sobriety checker.
* [mkbkp](#mkbkp) - Simple backups.
* [setmon](#setmon) - Switch to HDMI video/audio if any.
* [moin](#moin) - Play a random song from a YouTube playlist.

iwpick
------
Connects to a network. Authentication is not mandatory and only WPA2 is supported at the moment. It will works with no pain on any Linux system equipped with iwconfig, sdhcp and wpa_supplicant. Just run:

	$ sudo iwpick -e MyWirelessNetwork -k MyWPA2key

You may also use an aliases file (default ~/.networks), like this:

	home:MyHomeNetwork:MyHomePassord
	lucky:PublicNetwork
	
Then use the alias to connect:

	$ sudo iwpick -a home

*TIP: $ chmod og-rwx ~/.networks*

I'm considering if implement or not a flag to connects to the best possible network. The priority should be, descending order: private known networks, public known networks, public unknown networks. Networks with higher security and/or signal have higher priority. For now it's just an idea.

**Known issues**
- [sdhcp](http://git.2f30.org/sdhcp/) may wait forever if the network interface don't have an IP address assigned. The workaround is to assign an IP address to the interface before run iwpick. Note: the address must be a valid IP for the network; this may be changed by sdhcp.
- The `/etc/resolv.conf` is not update by iwpick so you'll have to put your nameservers there by hand. Alternatively, you can edit the iwpick script and remove the -d flag for sdhcp.

scrapthumb
----------
Get random images from Tumblr.  
Some sample usages in ~/.xinitrc:

	# Change wallpaper each 5 minutes 
	while true; do
		feh --bg-max $(~/sources/scripts/scrapthumb -rn1)
		sleep 5m
	done &

	# Change wallpaper each 5 minutes by storing the source into
	# /tmp/.scrapthumb (new with the -u flag)
	while true; do
		t="$(~/sources/scripts/scrapthumb -urn1)"
		src=$(echo $t |cut -d'|' -f1)
		img=$(echo $t |cut -d'|' -f2)
		feh --no-fehbg --bg-fill "$img"
		echo "$t" > /tmp/.scrapthumb

		sleep 5m
	done &

A more complex example with GIFs support (CPU intensive):

	# Change wallpaper each 5 minutes by storing the source into
	# /tmp/.scrapthumb (new with the -u flag). If the image is a GIF, then
	# it's split into frames which are then progressively set as
	# background, with 1ms of delay.
	while : ; do
		intval=300
		scrap="$(scrapthumb -urn1)"
		uri="$(echo "$scrap" |cut -d'|' -f2)"
		ext="$(echo "$uri" |rev |cut -d. -f1 |rev)"
		if [ "$ext" = "gif" ]; then
			d="$(mktemp -u /tmp/xinitrc.XXXXX)"
			mkdir -p "$d"
			convert "$uri" "$d/t.jpg"
			ts="$(date +'%s')"
			t=0
			while [ "$t" -lt "$intval" ]; do
				for jpg in "$d/"*.jpg; do
					feh --bg-fill --no-fehbg "$jpg"
					sleep 0.01
					t="$(echo "$ts - $(date +'%s')" |bc)"
					# never exceed $intval
					[ "$t" -ge "$intval" ] && break
				done
			done
			rm -rf "$d"
		else
			feh --bg-fill --no-fehbg "$uri"
		fi
		echo "$scrap" > /tmp/scrapthumb
		sleep "$intval"
	done &

This approach requires convert(1) from the ImageMagick suite.

You can achieve more extreme results by using scrapthumb in conjunction with
semi-automatic customization tools. Have a look at
[autotheme.sh](https://github.com/neeasade/autotheme.sh).

acpi
----
Notice the user if the battery charge is below 12% or suspend (in memory) the
system if the battery is below 9%. Make it runs each minute via cron like this:
```
* * * * * /path/of/acpi
```

dwmstatus
---------
Output useful informations suitable for a status bar. It's designed for dwm.
Sample output:
```
B:89%+ T:56C W:70% V:95%  (0.23 0.21 0.22 0.16) 25/09/2015 11:15
```

Here is a brief explanations of the fields:

- B: battery percentage. A trailing symbol indicates that AC power is plugged (+) or not (-)
- T: temperature in celsius degrees
- W: wireless signal
- V: volume. A trailing asterisk indicates if the mute is on
- Load average between parenthesis
- Date and time

To set the dwm bar you have to call xsetroot, like this:
```
xsetroot -name "$(/path/of/dwmstatus)"
```

It's easy to auto-update the bar by putting this in your ~/.xinitrc:
```
while true; do
	xsetroot -name "$(/path/of/dwmstatus)"
	sleep 30s
done &
```

spawncgi
--------
Spawn the CGI daemon.

pronunciask
-----------
Get pronunciations from forvo.com. Written for fun.

pkgsetup
--------
Managing pkgsrc bootstrapping and installation on non-NetBSD systems. Also
supports pkgsrc-wip.

whexsafe
--------
Generates the web safe colors list in hex and RGB formats.

swss
----
Simple web stack switch. It just tiny script to start and stop a group of
services which I use to deal with my web stack (redis, mysql, nginx, etc.).

droid
-----
Sane Android Development.

```
$ droid
Usage: droid <cmd>

Commands:
   setup        : creates the development environment
   install      : install the app into the device
   upload       : upload the app into the device
   build        : build the app (if no task, assumes assembleDebug)
   upgrade      : build and install the app (assembleDebug)
   deploy       : build and install the app (assembleRelease)
   init         : create a new project into the current directory
   clean        : remove all builds
   help         : print this help
```

browse
------
Little dmenu-based file browser which output the selected file (if any).

Sample usage:

```
$ mimeopen "$(/path/of/browse)"
```

For [dwm](http://dwm.suckless.org) users, it may come in handy a key binding
like this:

```
{ MODKEY|ShiftMask,		XK_o,      spawn,          SHCMD("mimeopen -n \"$(/path/of/browse)\"") },
```

diffmon
-------
Simple script which monitor a file and show lines that are changed since you
ran it.

sober
-----
Simple sobriety checker inspired by [DrunkGuard](https://github.com/jkingsman/DrunkGuard).

In a nutshell:
```
$ sudo rm -rf /
How much is 7 + 1? 8
How much is 5 + 9? 12
You're drunk, I'll take care of you.
```

Use sober by creating aliases in your ~/.${SHELL}rc file:

```
alias sudo="/path/of/sober sudo"
alias rm="/path/of/sober rm"
alias mv="/path/of/sober mv"
alias apt-get="/path/of/sober apt-get"
alias dpkg="/path/of/sober dpkg"
```

You may want to toggle this aliases at specific times (e.g. after work hours).
This is easily achievable with simple shell scripts.

mkbkp
-----
Copy all tracked files and git repositories into a directory. The stuff to
backup is taken from the file given as argument or ${HOME}/.mkbkprc if no
argument is given.

Sample configuration:
```
# default destination path if not given
BKPDIR="backup-$(hostname -s)-$(date +'%Y%m%d')"

# files and directories
NAMES="
${HOME}/myfiles
${HOME}/mail
${HOME}/.config/chromium/Default/Bookmarks
"

# git repositories
GITREPOS="
git@github.com:clamiax/scripts.git
"
```

mkbkp only supports backup of files, directories and git repositories. Though
it easy to add support for virtually anything. Just declare a function in your
configuration file and add its name into the $SUPPORT variable. See bkp_names
and bkp_gitrepos in [mkbkp](src/mkbkp) to learn more.

setmon
------
Detects connected screens and set proper video and audio output. It uses xrandr
and pulseaudio. I wrote it to easily switch forth and back from my HDMI TV.
Change it to fit your needs.

moin
----
Play a random song from a YouTube playlist. The playlist is specified by its
ID, if none is given $DEFLIST is used. Default player is mpv, refine the
$PLAYER and $PLAYER_OPTS variables to fit your needs. You may want to wake up
in the morning by putting the following in your crontab:

```
# Plays from monday to friday at 08:00am
0 8 * * 1-5 /path/of/moin
```

Where to take the playlist ID? It's specified by the "list" parameter of the
URL. For example, if the URL of your playlist is the following:

```
https://www.youtube.com/watch?v=xvIuuKVwY_8&list=RDEMWmz07MSPRGna5rHl5FWPRw&index=40
```

Then the ID is: RDEMWmz07MSPRGna5rHl5FWPRw
