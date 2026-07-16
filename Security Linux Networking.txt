Security (Linux + Networking)

Types of attackers:
	White hat: The good guy
	Black hat: Unethical hacker
	Grey hat: Occasional unethical hacker
	
Steps of an attack: Reconnaissance->understanding the system->Gain access->Collect info->create backdoor->attack/inform
To avoid this we use "Defense in Depth" design philosophy while building a network to protect the structure against both mass attacks and structurally planned attacks. E.g. using encryption, firewalls, Host-based Intrusion prevention system(monitors hosts to pick out malicious actions and files), authentications.

---------------------------------------------------------------------

IP Spoofing: attackers IP appears as one of the trusted IPs allowed in network. Attacker might use layer 3 TCP to become trusted (TCP level 4 is challenging).

TCP:
	1. Original client sends SYN to destination
	2. Dest sends ACK with both original seq number and dest seq no
	3. Original client sends a ACK to dest and then TCP comms are open

>>>Basically the attacker needs to know the TCP sequence numbers and then he can send a properly construct ACK segment to destination (before the original ACK reaches).<<<
(The attacker can be on the same subnet then its nonblind spoofing, but if he is not then it is blind spoofing which is more difficult [IP source routing is used]). In this you can specify a different routing path to be taken by two endpoints.

Solution:
	* Use ACL on router interfaces. (Matches IP addresses if some host tries to impersonate).
	* Encrypt traffic between devices through an IPsec tunnel.
	* Overkill would be using a cryptographic tunnel.

---------------------------------------------------------------------

Confidentiality attack:
	Packet capture
	Ping sweep and port scan
	dumpster diving: Company conf info thrown
	EMI interception: Unshielded twisted pair
	Wiretapping
	Social engineering
Integrity attack: Attempts to alter important data.
	Trojan horse: a program impersonating the real one used to steal data
	Keylogger: Obvious.
	Brute force/Dictionary
Availability attack:
	Dos
	DDoS
	TCP SYN flood
Smurf attack:
	Electrical disturbance
	Gas
	Temp
	
---------------------------------------------------------------------

Server hardening: Practice of changing server config at all layers to minimize attacks and increase resistance to penetration attempts. Basically minimizing the number of vulnerabilities.

Web app server hardening:
	Enable TLS/SSL: Basically enabling HTTPS to encrypt the comms between client and server. The web host needs to create a certificate and configure it.
	Restrict what values clients can send in the 'Host' HTTP request header. As the HTTP request header contains hostname and port number as we need to restrict and specify the hosts you want to allow Server to serve.
