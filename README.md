scripts
=======
Useful shell scripts.

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

	# Change wallpaper each 5 minutes by storing the source
	# into /tmp/.scrapthumb (new with the -u flag)
	while true; do
		t="$(~/sources/scripts/scrapthumb -urn1)"
		src=$(echo $t |cut -d'|' -f1)
		img=$(echo $t |cut -d'|' -f2)
		feh --no-fehbg --bg-fill "$img"
		echo "$t" > /tmp/.scrapthumb

		sleep 5m
	done &

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

B: battery percentage. A trailing symbol indicates that AC power is plugged (+) or not (-)
T: temperature in celsius degrees
W: wireless signal
V: volume. A trailing asterisk indicates if the mute is on
Load average between parenthesis
Date and time

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
