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
		feh --bg-scale $(~/sources/scripts/scrapthumb -rn1)
		sleep 5m
	done &

spawncgi
--------
Spawn the CGI daemon.
