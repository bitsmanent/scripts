scripts
=======
Useful shell scripts.

index
-----
* [txthole](#txthole) - Simple netcat-based pastebin server
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
* [dbrowse](#dbrowse) - Little dmenu-based file browser.
* [diffmon](#diffmon) - Diff-based file monitor.
* [sober](#sober) - Simple sobriety checker.
* [mkbkp](#mkbkp) - Simple backups.
* [setmon](#setmon) - Switch to HDMI video/audio if any.
* [moin](#moin) - Play a random song from a YouTube playlist.
* [fetchpic](#fetchpic) - Fetch a random pic from a random blog (of a given list).

txthole
-------
Store and retrieve text stream. Given the script is up and running, here's a sample usage:

	$ echo Hello |nc your.host 2023
	http://your.host/GmKwL
	echo GmKwL |nc your.host 2023
	$ echo GmKwL |nc your.host 2023
	Hello

Of course you can also paste files:

	$ cat /tmp/t |nc your.host 2023

You can set hostname when starting txthole, like this:

	$ TXTHOLE_PUBHOST=my.public.host txthole &

If it's unset then txthole will only output the code instead of the whole command:

	$ txthole &
	$ echo Hello |nc your.host 2023
	A1Syh

This should be enough for most use cases.

To serve content over HTTP, use something like this (nginx):

	server {
		root /tmp/pastes/;
		server_name your.host;

		location / {
			rewrite ^/([-.a-zA-Z0-9]+)$ "/txthole.$1.paste" last;
		}

		location ~ /txthole\.([-.a-zA-Z0-9]+)\.paste {
			add_header Content-Type text/plain;
		}
	}

Take care of applying the right user/permission to the pastes directory:

	# chgrp www-data /tmp/pastes
	# chmod g+srx /tmp/pastes # s used to keep group


iwpick
------
Connects to a network. Authentication is not mandatory and only WPA2 is supported at the moment. It will works with no pain on any Linux system equipped with iwconfig, sdhcp and wpa_supplicant. Just run:

	$ sudo iwpick -e MyWirelessNetwork -k MyWPA2key

You may also use an aliases file (default ~/.networks), like this:

	home:MyHomeNetwork:MyHomePassord
	lucky:PublicNetwork
	
Then use the alias to connect:

	$ sudo iwpick -a home

Connect to the first known network available:

	$ sudo iwpick -p

*TIP: $ chmod og-rwx ~/.networks*

I'm considering if implement or not a flag to connects to the best possible network. The priority should be, descending order: private known networks, public known networks, public unknown networks. Networks with higher security and/or signal have higher priority. For now it's just an idea.

**Known issues**
- [sdhcp](http://git.2f30.org/sdhcp/) may wait forever if the network interface don't have an IP address assigned. The workaround is to assign an IP address to the interface before run iwpick. Note: the address must be a valid IP for the network; this may be changed by sdhcp.
- The `/etc/resolv.conf` is not update by iwpick so you'll have to put your nameservers there by hand. Alternatively, you can edit the iwpick script and remove the -d flag for sdhcp.

scrapthumb
----------
Get random images from Tumblr.  

Note: currently not working due to the recent Tumblr redirect to the new
privacy policy, which you need to accept.

Some sample usages in ~/.xinitrc:

	# Change wallpaper each 5 minutes 
	while true; do
		feh --bg-max $(/path/of/scrapthumb -rn1)
		sleep 5m
	done &

	# Change wallpaper each 5 minutes by storing the source into
	# /tmp/.scrapthumb (new with the -u flag)
	while true; do
		t="$(/path/of/scrapthumb -urn1)"
		src=$(echo $t |cut -d'|' -f1)
		img=$(echo $t |cut -d'|' -f2)
		feh --no-fehbg --bg-fill "$img"
		echo "$t" > /tmp/.scrapthumb

		sleep 5m
	done &

If the image is in GIF format then the first frame is used (i.e. no playing at
all). You can solve this by splitting frames up with convert(1) and set each of
them as background with an interval of ~0.01 seconds. A better approach would
be to use mpv with the --wid=0 parameter which is less CPU intensive.

You can achieve more extreme results by using scrapthumb in conjunction with
semi-automatic customization tools. Have a look at
[autotheme.sh](https://github.com/neeasade/autotheme.sh).

acpi
----
Notice the user if the battery charge is below 12% or suspend (in memory) the
system if the battery is below 9%. Make it runs each minute via cron like this:
```
* * * * * sudo -i /path/of/acpi
```

It uses xmessage(1) and play(1) to alert the user.

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

dbrowse
------
Little dmenu-based file browser which output the selected file (if any).

Sample usage:

```
$ mimeopen "$(/path/of/dbrowse)"
```

For [dwm](http://dwm.suckless.org) users, it may come in handy a key binding
like this:

```
{ MODKEY|ShiftMask,		XK_o,      spawn,          SHCMD("mimeopen -n \"$(/path/of/dbrowse)\"") },
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
The [moin](src/moin) script takes a random song from the specified YouTube
playlist and play it. The default player is mpv but you can refine the $PLAYER
and $PLAYER_OPTS variables to fit your needs. You may want to wake up in the
morning by putting the following in your crontab:

```
# Plays from monday to friday at 08:00am
0 8 * * 1-5 /path/of/moin <YOUR-API-KEY> <PLAYLIST-ID>
```

Both the API key and the playlist ID are required. If you don't have a key then
[make one here](https://console.developers.google.com/apis/credentials).
The playlist ID is specified by the "list" parameter of the URL. For example,
if the URL of your playlist is the following:

```
https://www.youtube.com/watch?v=xvIuuKVwY_8&list=RDEMWmz07MSPRGna5rHl5FWPRw&index=40
```

Then the ID is: RDEMWmz07MSPRGna5rHl5FWPRw

fetchpic
--------
Fetch a random pic from a random blog (of a given list).

This is [scrapthumb](#scrapthumb) rewritten from scratch and better. Now the
Tumblr API is used so you need an [api key](https://www.tumblr.com/oauth/apps).

In a nutshell:

```
# scrap="$(APIKEY=Your-Api-Key fetchpic)"
# uri="$(echo "$scrap" |cut -d'|' -f2)"
# feh --bg-fill --no-fehbg "$uri"
```

Put this in your `.xinitrc` to change the wallapaper each 5 minutes and append
the pic URL and blog name into `/tmp/fetchpic`.

```
while : ; do
	scrap="$(APIKEY=Your-Api-Key fetchpic)"
	uri="$(echo "$scrap" |cut -d' ' -f2)"
	echo "$scrap" >> /tmp/fetchpic
	feh --bg-fill --no-fehbg "$uri"
	sleep 300
done &
```

Enjoy.
