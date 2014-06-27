scripts
=======
Useful shell scripts.

acpi
----
Suspend the system if battery is critical.

dwmstatus
---------
Draw useful informations to the dwm bar.

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

spawncgi
--------
Spawn the CGI daemon.

vegask
------
Get vegan people from Wikipedia. Written for fun.

pronunciask
-----------
Get pronunciations from forvo.com. Written for fun.
