scripts
=======
Useful shell scripts.

iwpick
------
Connects to a network. Authentication is not mandatory and only WPA2 is supported at the moment. It will works with no pain on any Linux system equipped with iwconfig, dhclient and wpa_supplicant. Just run:

	$ sudo iwpick -e MyWirelessNetwork -k MyWPA2key

You may also use an aliases file (default ~/.networks), like this:

	home:MyHomeNetwork:MyHomePassord
	lucky:PublicNetwork
	
Then use the alias to connect:

	$ sudo iwpick -a home

*TIP: $ chmod og-rwx ~/.networks*

I'm considering if implement or not a flag to connects to the best possible network. The priority should be, descending order: private known networks, public known networks, public unknown networks. Networks with higher security and/or signal have higher priority. For now it's just an idea.

scrapthumb
----------
Get random images from Tumblr.  
Some sample usages in ~/.xinitrc:

	# Change wallpaper each 5 minutes 
	while true
	do
		feh --bg-max $(~/sources/scripts/scrapthumb -rn1)
		sleep 5m
	done &

	# Change wallpaper each 5 minutes by storing the source
	# into /tmp/.scrapthumb (new with the -u flag)
	while true
	do
		t="$(~/sources/scripts/scrapthumb -urn1)"
		src=$(echo $t |cut -d'|' -f1)
		img=$(echo $t |cut -d'|' -f2)
		feh --no-fehbg --bg-fill "$img"
		echo "$t" > /tmp/.scrapthumb

		sleep 5m
	done &

acpi
----
Suspend the system if battery is critical.

dwmstatus
---------
Draw useful informations to the dwm bar.

spawncgi
--------
Spawn the CGI daemon.

vegask
------
Get vegan people from Wikipedia. Written for fun.

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
