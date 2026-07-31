* Daemon: Process that runs in the background. E.g. Apache2, Nginx, MySQL. They do not need user interaction to run and start at boot to run continuously.
* Before systemd there was SysVinit. systemd introduced parallel service starting and added declarative unit files instead of shell scripts.
* Commands:
	* systemctl list-units [list all units]
	* systemctl start name
	* systemctl stop name
	* systemctl enable name: always starts at boot
	* systemctl disable name: does not start at boot
	* Also included: restart, status and reload.
	* journalctl -u nginx
	* sudo systemctl list-unit-files --type=service --state=running [list services in running state]