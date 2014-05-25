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
Example usage in ~/.xinitrc:

	# Change wallpaper each 5 minutes 
	while true
	do
		feh --bg-max $(~/sources/scripts/scrapthumb -rn1)
		sleep 5m
	done &

spawncgi
--------
Spawn the CGI daemon.

vegask
------
Get vegan people from Wikipedia. Written for fun.
