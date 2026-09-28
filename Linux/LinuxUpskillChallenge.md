https://linuxupskillchallenge.org/

Day 1:
* ssh (username)@(ip) -> password
* Get info about server:
	* lsb_release -a : linux version
	* uname -a : system info
	* uptime : literally uptime
	* whoami : hostname/username of server
* **Understanding Swap**: Part of machine's virtual memory which is a combination of accessible physical memory (RAM). Used when physical memory is insufficient so inactive pages are moved into swap to make space in RAM. E.g. for 16GB RAM, swap should be 20-32GB (With hibernation). You can also adjust swappiness.
Day 2:
* Nothing much here
Day 3:
* It is common practice now not to login as root and run commands on the server. Instead we should give permissions to trusted users (make them sudoers) to run commands using sudo command.
* use "last (username)" command to see everytime you login.
* Check for failed login attampts : sudo lastb
* Check the last few times sudo was used by typing: sudo journalctl -e /usr/bin/sudo
* Check data and time configuration : timedatectl
Day 4: