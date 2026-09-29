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
* (you could also see auth.log wot see recent logins and sudo usages)
* Check for failed login attampts : sudo lastb
* Check the last few times sudo was used by typing: sudo journalctl -e /usr/bin/sudo
* Check data and time configuration : timedatectl
Day 4:
* Search for packages before downloading: apt search "app name"
* apt is more structured for new user and provides the user with only necessary options to manager packages unlike apt-get which is older and is more low level for the experienced user.
* Package management:
	* sudo apt update: updates list in /etc/apt/sources.list.d.
	* sudo apt remove nmap: add --purge to remove configuration files as well.
* Install "aptitude" to manage packages with a proper GUI. Run "sudo aptitude" to run it.
* Logs : /var/log/dpkg.log
* dpkg is package manager for Debian based systems.
* List all packages : dpkg -l
Day 5:
* You can have several shell sessions open at the same time using "terminal multiplexer".
* https://www.thegeekyway.com/what-are-dotfiles/ : To learn more about dotfiles.
Day 6:
* nano was written for DOS back in the 1980's. But real admins use Vim (descendant of Vi).
* Vim:
	* Press Esc multiple times to come back to normal mode.
	* :q! to exit without saving
	* h j k l to move around.
	* u will undo the last action
	* If you press 3 -> 3 -> d -> d delete 33 lines below it.
	* G to get to the bottom of the file.
	* gg to get to the top of the file.
Day 7:
* https://sadservers.com/ : To try out different scenarios.
Day 8:
* "less (file location)" is used to see the contents of the file in a scroll able manner.
* "|" is a pipe symbol used to take output of one command and pipe it into another.
Day 9:
* Show which ports are open on which interfaces: sudo ss -ltp
* use "nmap (ip)" to scan open ports on any ip.
* Use ufw to enable to disable ports (uncomplicated firewall).
* You can also try reconfiguring services to non standard ports.
* ss commands:
	* ss : listing all connections
	* ss -a : all listening and non listening ports
	* ss -l : only listening sockets
	* ss -t : all tcp connections
	* ss -ua : all UDP connections
	* ss -lu : all listening UDP
	* ss -p : show PID of sockets
	* ss -s : show summary
	* ss -4 : ipv4 (6 for ipv6)
* https://danielmiessler.com/blog/no-moving-your-ssh-port-isnt-security-by-obscurity : Why changing your SSH port number could be useful after all.
* **Port knocking** is a stealth method to externally open ports that, by default, the firewall keeps closed. It works by requiring connection attempts to a series of predefined closed ports. With a simple port knocking method, when the correct sequence of port "knocks" (connection attempts) is received, the firewall opens certain port(s) to allow a connection. 