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
Example usage:

# ~/.xinitrc
while true
do
	feh --bg-scale $(~/sources/scripts/scrapthumb -n1 -s http://desktopwallpaperproject.tumblr.com)
	sleep 1m
done &

spawncgi
--------
Spawn the CGI daemon.
