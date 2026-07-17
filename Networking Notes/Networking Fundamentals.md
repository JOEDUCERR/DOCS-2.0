Networking Fundamentals further:

-----------------------------------------------------------
Stuff left: Cisco router, ICMP, HSRP, IPv6, etc
-----------------------------------------------------------

OSI: Open Systems Interconnection

DHCP: Dynamic Host Configuration Protocol. Port number 67 for server and 68 for client.
	Dynamic: Automatically gets assigned as per availability.
	Host: Address of each machine
It maintains a pool of available IP addresses and assigns as per need. Also provides subnet mask, default gateway and DNS. It first discovers (broadcast and discover packet), offers (receives offer packet and then sends request packet to which the main server responds first), request and then Acknowledges (Ack packet).

-----------------------------------------------------------

APIPA: Automatic Private IP Addressing. Helps pcs to automatically configure IP address and subnet mask when DHCP server is unreachable.
	Range: 169.254.0.1-169.254.255.254 (255.255.0.0)
Enables host to still talk to other hosts in the same network. In this state it still keeps checking for DHCP server if it comes online.

-----------------------------------------------------------

STP: Spanning Tree Protocol (IEEE 802.D). Ensures there are no loops in the network between switches. If there is same info sent between all switches it blocks the one port on switches to prevent it from going back to the source and looping.
<<<Packet -> Root switch -> Designated switch (desig port which is selected using the best path to share info between switches) -> Nonroot switches>>>
	BPDU: Bridge Protocol Data Unit. used to share STP info with switches about who is the root, designated and nonroot switches. Each switch has 8 byte switch ID.
If the bridge priority is same on all switches, MAC address is used to break the tie (lowest MAC will become root). We should avoid this as it can cause low performance.
Also we select a root secondary as a backup switch incase the primary root bridge fails.
RSTP: Rapid STP (802.1Q). Faster than standard STP.

-----------------------------------------------------------

IP routing: Process of sending packets to host from one network to another remote network. Process is done by routers. For this reason Default gateways are used. "Route tables" are stored in RAM of router having info about paths to destination networks.

Routing protocols: These are configured on routers. These can be dynamic unlike static IPv4 routing and can be learned via routing protocols. RIP, EIGRP, OSPF. IPv6 uses RIPng, EIGRP, etc.
	Distance vector: sends entire routing table. RIP Uses number of hops as metric. OSPF uses SPF shortest path first algo to find the best route. Meanwhile EIGRP is an advanced distance vector.
	Link state: sends only link state info
	
Loopback Interface/address: A loopback interface is a virtual interface in our network device that is always up and active after it has been configured. Like our physical interface, we assign a special IP address which is called a loopback address or loopback IP address.

-----------------------------------------------------------

VLANS: Virtual LANs. Logical groupings of devices in the same broadcast domain. Configured on switches. Reduces security risk.
To identify VLAN using a packet a tag is used. This is done by a protocol called Inter-Switch link (only in old cisco switches). Otherwise 802.1Q is used for tagging in Cisco switches.
When trying to make a VLAN for SIP phones we need to define two VLANs on the switch port – data VLAN and voice VLAN.

InterVLAN communication: Done using a Level 3 device like router or Cisco catalyst switches. But another workaround is to use one router interface with trunking enable called router on a stick (ROAS).

-----------------------------------------------------------

EtherChannel: Bundling of physical interfaces to create a single logical link. Often used between multi switches and routers. Can make upto 16 but only 8 can be active at a time.

-----------------------------------------------------------

DHCP Snooping: Prevents unauthorized DHCP servers from accessing your network and man-in-the-middle attacks. This is done in layer 2 switches and trusted ports should be configured manually.

Dynamic ARP Inspection: It is to avoid main in the middle attacks as hackers can get the mac address and obtain info meant for the target pc. They send a unauthorized ARP reply through which the ARP mapping is updated. DARP Inspection compares incoming ARP packet with existing table.

802.1x Authentication: has supplicant, authenticator and authentication server.

Port security: network admin can associate specific MAC with interface so an unknown entitiy cannot connect to the network.

-----------------------------------------------------------

ACL: Access control list is used to identify which addresses have how much access.

NAT: Network Address Translation. Translating private IP address to a single public IP address. There is static and dynamic (automatic) NAT. For dynamic we give local addresses and a pool of global addresses.

PAT: Port Address Translation is used to connect a single IP address for all private IP addresses, but a different port is assigned to each private IP. (Most common) It is uused to support many hosts with a few public IPs. (NAT Translation Table).

-----------------------------------------------------------

IPv6:
* 3.4 x 10^38 addresses.
* 128 bit addresses instead of 32.
* IPSec security built into IPv6.
* Simple headers than IPv4
* No NAT need as too many addresses.
* autmatic configuration (stateless)
* 40 bytes long packet header/
* works the same way in routing like IPv4.
* This uses 8 groups of 4 hexadecimal digits separated by colons.
* we can assign ipv6 addresses using DHCPv6.

-----------------------------------------------------------


