#!/bin/sh
# simple pastebin server
#
# Sample usage (assuming ./txthole.sh is running):
# cat /my/file |nc host 2023 # put file
# echo "my text" |nc host 2023 # put text
# echo paste-code |nc host 2023 # get

pastes="/tmp/pastes"
fifo="/tmp/txthole.fifo"
port="2023"

run() {
	paste="$1"
	label="$(basename "$paste" |cut -d. -f2)"

	read s
	p="$pastes/txthole.$s.paste"
	if [ -f "$p" ]; then
		cat "$p"
	else
		echo "echo $label |nc your.host $port"
	fi
}

main() {
	mkdir -p "$pastes"
	rm -f "$fifo"
	mkfifo "$fifo"
	while : ; do
		paste="$(mktemp -q "$pastes/txthole.XXXXX.paste")"
		cat "$fifo" |tee -a "$paste" | run "$paste" | nc -4Nl "$port" > "$fifo"
		[ "$(wc -c "$paste" |cut -d' ' -f1)" -eq 0 ] && rm -f "$paste"
	done
	rm -f "$fifo"
}

main
