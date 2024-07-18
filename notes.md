# CCNA Notes
**What is a network?** 
A system of interconnected entities, such as computers, servers, routers, etc. that are linked together to share resources,
information, or services. 

**OSI Model**

Application
Presentation
Session
Transport
Network
Data Link
Physical

Physical Layer - Defines physical characteristics of the medium used to transfer data between devices. (e.g. voltage level, cable specifications,
max transmission distances, physical connectors). 

Data Link Layer - Provides node-to-node connectivity and data transfer (e.g. PC -> switch, switch -> router, router -> router). Defines how data
is formatted for transmission over a physical medium. Detects and corrects Physical Layer error.

**Protocol Data Units (PDUs)**

Data 
Data | L4 Header    Segment
Data | L4 Header | L3 Header    Packet
L2 Trailer | Data | L4 Header | L3 Header | L2 Header   Frame


Ethernet Frame

Ethernet Header -> Preamble | Start Frame Delimiter | Destination | Source | Type
Packet
Ethernet Trailer -> Frame Check Sequence

Preamble: 
7 bytes long (56 bits) 
Alternating 1's and 0's
Allows devices to synchronize their receiver clocks

Start Fram Delimiter (SFD):
1 byte long (8 bits)
10101011
Marks the end of the preamble, and the beginning of the rest of the frame.

Destination & Source:
Indicate the devices sending and receiving the fram
Consist of the destination and source 'MAC address'
MAC = Media Access Control
6 byte (48-bit) address of the physical device

Type / Length:
2 byte (16-bit) field
A value of 1500 or less in this field indicates the LENGTH of the encapsulated packet (in bytes)
A value of 1536 or greater in this field indicates the TYPE of the encapsulated packet, and the length is determined via other methods

Frame Check Sequence (FCS):
4 bytes (32 bits) in length
Detects corrupted data by running a 'CRC' algorithm over the received data
CRC = 'Cyclic Redundancy Check'

26 bytes in total (header + trailer)

MAC Address
6-byte physical address assigned to the device when it is made
A.K.A. 'Burned-In Address' (BIA)
Is globally unique
The first 3 bytes are the OUI (Organizationally Unique Identifier), which is assigned to the company making the device
The last 3 bytes are unique to the device itself
Written as 12 hexadecimal characters

Dynamic MAC addresses are removed from the MAC address table after 5 minutes of inactivity. 

Preamble + SFD is usually not considered part of the Ethernet header

Minimum size for an Ethernet frame is 64 bytes. 
Minimum payload size is 46 bytes.
If the payload is less than 46 bytes, padding bytes are added

Address Resolution Protocol (ARP):
Used to desover the Layer 2 address (MAC address) of a known Layer 3 address
Consists of two messages: ARP Request & ARP Reply
ARP Request is broadcast = sent to all hosts on the network
ARP Reply is unicast = sent only to one host (the host that sent the request)

FFFF.FFFF.FFFF = broadcast MAC address

ping:
a network utility that is used to test reachability
measures round-trip time
uses two messages: ICMP Echo Request & ICMP Echo Reply
command: ping <ip-address>

*show mac address-table*
*clear mac address-table dynamic address <mac-address>*
*clear mac address-table dynamic interface <interface-id>*

Network Layer - Provides connectivity between end hosts on different networks (outside of the LAN)
Provides logical addresing (IP addresses)
Provides path selection between source and destination

IP addresses are 32 bits in length

**IPv4 Address Classes**
| Class | First Octet | First octet numeric range | mask                              |
|-------|-------------|---------------------------|-----------------------------------|
| A     | 0xxxxxxx    | 0-127                     | /8                                |
| B     | 10xxxxxx    | 128-191                   | /16                               |
| C     | 110xxxxx    | 192-223                   | /24                               |
| D     | 1110xxxx    | 224-239                   | (multicast addresses)             |
| E     | 1111xxxx    | 240-255                   | (reserved/experimental addresses) |

Loopback Addresses
Address range 127.0.0.0 - 127.255.255.255
Used to test the 'network stack' on the local device

Host portion of the address is all 0's = Network Address
The network address CANNOT be assigned to a host

Host portion of the address is all 1's = Broadcast Address
The broadcast address CANNOT be assigned to a host

Max number of hosts = 2^(host bits) - 2

Cisco router interfaces are administratively down by default
Cisco switch interfaces ar eNOT administratively down by deafult

*interface <interface> = enter interface configuration mode*
*ip address <ip address> <mask> = assign ip address and subnet mask*
*no shutdown = enable interface*
*show interfaces = displays information about interfaces*

*hostname <name> = configure's devices hostname*
*do show ip interface brief = view interfaces/ip addresses/status/protocol*

*show interfaces status = shows port/name/status/vlan/duplex/speed type*

*interface range f0/x - y (easy way to configure multiple interfaces)*

Half duplex: The device cannot send and receieve data at the same time. If it is receiving
a fram, it must wait before sending a frame.

Full duplex: Thee device can send and receive data at the same time. It does not have to wait.

In modern day networks, half duplex is rarely/never used.

CSMA/CD (Carrier Sense Multiple Access with Collision Detection)
Before sending frames, devices 'listen' to the collision domain until they detect that other devices are not sending.
If a collision do occur, the device sends a jamming signal to inform the other devices that a collision happened. 
Each device will wait a random period of time before sending frames again.
The process repeats.

Speed/Duplex Autonegotiation
Interfaces that can run at different speeds (10/100 or 10/100/1000) have default settings of speed auto and duplex auto
Interfaces 'advertise' their capabilities to the neighboring device, and they negotiate the best speed and duplex settings they are both capable of.

What if autonegotiation is disabled on the device connect to the switch?

SPEED: the switch will try to sense the speed that the other device is operating at.
If it fails to sense the speed, it will use the slowest supported speed.

DUPLEX: If the speed is 10 or 100 Mbps, the switch will use half dupelex. 
If the speed is 1000 Mbps or greater, use full duplex.

ERRORS
Runts: Frames that are smaller than the minimum frame size (64 bytes)
Giants: Frames that are larget the the maximum frame size (1518 bytes)
CRC: Frames that failed the CRC check (in the Ethernet FCS trailer)
Frame: Frames that have an incorrect format (due to an error)
Input errors: Total of various counters, such as the above four
Output errors: Frames the switch tried to send, but failed due to an error

### IPv4 Header Fields

Version - 4 bits
Identifies the version of IP used.
IPv4 = 4 (0100)
IPv6 = 6 (0110)

Internet Header Length (IHL) - 4 bits
The final field of the IPv4 header (Options) is variable in length, so this field is necessary to indicate the total length of the header.
Identifies the length of the header in 4-byte increments.
Value of 5 = 5x4-bytes = 20 bytes
Minimum value is 5 (= 20 bytes)
Maximum value is 15 (15 x 4 bytes = 60 bytes)
Minimum IPv4 Header length = 20 bytes
Maximum IPv4 Header length = 60 bytes

DSCP 'Differentiated Services Code Point' - 6 bits
Used for QoS (Quality of Service)
Used to prioritize delay-sensitive data (streaming voice, video, etc.)

ECN 'Explicit Congestion Notification' - 2 bits
Provides end-to-end (between two endpoints) notification of network congestion without dropping packets. 
Optional feature that requires both endpoints, as well as the underlying network infrastructure, to support it. 

Total Length field - 16 bits
Indicates the toal length of the packet (L3 header + L4 segment)
Measured in bytes (not 4-byte increments like IHL)
Minimum value of 20 (=IPv4 header with no encapsulated data)
Maximum value of 65535 (maximum 16-bit value)

Identification field = 16 bits
If a packet is fragmented due to being too large, this field is used to identify which packet the fragment belongs to.
All fragments of the same packet will have their own IPv4 header with the same value in this field.
Packets are fragmented if larger than the MTU (Maximum Transmission Unit)
The MTU is usually 1500
Remember the maximum size of an Ethernet frame?
Fragments are reassembled by the receiving host

Flags field - 3 bits
Used to control/identify fragments
Bit 0: Reserved, always set to 0
Bit 1: Don't Fragment (DF bit), used to indicate a packet that should not be fragmented
Bit 2: More Fragments (MF bit), set to 1 if there are more fragments in the packet, set to 0 for the last fragment
*Unfragmented packets will always have their MF bit set to 0

Fragment Offset field - 13 bits
Used to indicate the position of the fragment within the original, unfragmented IP packet.
Allows fragmented packets to be ressembled even if the fragments arrive out of order.

Time to Live field - 8 bits
A router will drop a packet with a TTL of 0
Used to prevent infinite loops
Originally designed to indicate the packet's maximum lifetime in seconds
In practice, indicates a 'hop count': each time the packet arrives at a router, the router decreases the TTL by 1.
* Recommended TTL by default is 64

Protocol field - 8 bits
Indicates the protocol of the encapsulated L4PDU
Value of 6: TCP
Value of 17: UDP
Value of 1: ICMP
Value of 89: OSPF (dynamic routing protocol)

Header Checksum - 16 bits
A calculated checksum used to check for errors in the IPv4 header.
When a router receives a packet, it calculates the checksum of the header and compares it to the one in this field of the header.
If they do not match, the router drops the packet.
Used to check for errors only in the IPv4 header.
IP relies on the encapsulated protocol to detect errors in the encapsulated data.
Both TCP and UDP have their own checksum fields to detect errors in the encapsulated data.

Source & Destination IP Address field - 32 bits (each)

Options field - 0 - 320 bits
Rarely used.
If the IHL field is greater than 5, it means that Options are present.

### Routing Fundamentals

Routing is the process that routers use to determine the path that IP packets should take over a netowrk to reach their destination.
Routers store routes to all of their known desitnations in a routing table.
When routers receive packets, they look in the routing table to find the best route to forward that packet.

There are two main routing methods (methods that routers use to learn routes):
Dynamic Routing: Routers use dynamic routing protocols (ie. OSPF) to share routing information with each other automatically and build their routing tables.
Static Routing: A network engineer/admin manually configures routes on the router.

A route tells the router: to send a packet to destination X, you should send the packet to next-hop Y
or , if the destination is directly connected to the router, send the packet directly to the destination
or , if the destination is the router's own IP address, receive the packet for yourself

WAN (Wide Area Network) = a network that extends over a large geographical area)

show ip route -> command to view the routing table

L - local - a route to the actual IP address configured on the interface (with a /32 netmask)
C - connected - a route to the network the interface is connected to. (with the actual netmask configured on the interface)

When you configure an IP address on an interface and enable it with no shutdown, 2 routes (per interface) will automatically
be added to the routing table:
- a connected route
- a local route

A route matches a packet's destination if the packet's destination IP address is part of the network specified in the route.

Route selection - it will choose the most specific matching route
Most specific matching route - the matching route with the longest prefix length.
Local route = keep packet, don't forward

To send packets outside of local network, end hosts must send packets to their default gateway
The default gateway configuration is also called a default route.
It is a route to 0.0.0.0/0 = all netmask bits set to 0. Includes all addresses from 0.0.0.0 to 255.255.255.255

The default route is the least specific route possible, because it includes all IP addresses.
End hosts usually have no need for any more specific routes. They just need to know: to send packets outside
of my local network, I should send them to my default gateway.

*ip route <ip-address> <netmask> <next-hop>*
*ip route <ip-address> <netmask> <exit-interface>*

A default route is a route to 0.0.0.0/0 is the least specific route possible; it includes every possible destination IP address.
If the router doesn't have any more specific routes that match a packet's destination IP address, the router will forward the packet using the default route.
A default route is often used to direct traffic to the Internet. More specific routes are used for destinations in the internal corporate network.
Traffic to destinations outside of the internal network is sent to the Internet.

Only class A, B, C can be assigned to a device as an IP address.

The IANA (Internet Assigned Numbers Authority) assigns IPv4 addresses/networks to companies based on their size.
For example, a very large company might receive a class A or class B network, while a small company might receive a class C network.
However, this led to many wasted IP addresses.

CIDR (Classleess Inter-Domain Routing), the requirements of Class A, Class B, and Class C were removed. This allowed larger networks to be split into smaller
networks, allowing greater efficiency.

| Dotted Decimal  | CIDR Notation |
|-----------------|---------------|
| 255.255.255.128 | /25           |
| 255.255.255.192 | /26           |
| 255.255.255.224 | /27           |
| 255.255.255.240 | /28           |
| 255.255.255.249 | /29           |
| 255.255.255.252 | /30           |
| 255.255.255.254 | /31           |
| 255.255.255.255 | /32           |

2^x = number of subnets (x = number of 'borrowed' bits)

**Subnets/Hosts (Class B)**

| Prefix Length | Number of Subnets | Number of Hosts |
|---------------|-------------------|-----------------|
| /17           | 2                 | 327366          |
| /18           | 4                 | 16382           |
| /19           | 8                 | 8190            |
| /20           | 16                | 4094            |
| /21           | 32                | 2046            |
| /22           | 64                | 1022            |
| /23           | 128               | 510             |
| /24           | 256               | 254             |
| /25           | 512               | 126             |
| /26           | 1024              | 62              |
| /27           | 2048              | 30              |
| /28           | 4096              | 14              |
| /29           | 8192              | 6               |
| /30           | 16384             | 2               |
| /31           | 32768             | 0 (2)           |
| /32           | 65536             | 0 (1)           |

VLSM (Variable-Length Subnet Masks) is the process of creating subnets of different size, to make your use of network addresses more efficient.

Steps:
1) Assign the largest subnet at the start of the address space.
2) Assign the second-largest subnet after it.
3) Repeat the process until all subnets have been assigned.

A LAN is a single broadcast domain, including all devices in that broadcast domain.

A broadcast domain is the group of devices which will receive a broadcast frame (destination MAC FFFF.FFFF.FFFF) sent by any one of the members.

A switch will not forward traffic between VLANs, including broadcast/unknown unicast traffic.
Switch does not perfom inter-VLAN routing. It must send the traffic through the router.

VLANs are configured on switches on a per-interface basis.
VLANs logically seperate end hosts at Layer 2. 

Switches do not forward traffic directly between hosts in different VLANs.

*show vlan brief* - shows vlans that exists in the switch and interfaces that are in vlans
VLANs 1,1002-1005 exist by default and cannot be deleted.

**VLAN CONFIGURATION**

An access port is a switchport which belongs to a single VLAN, and usually connects to end hosts like PCs.
Switchports which carry multiple VLANs are called 'trunk ports'.

*interface range gx/x - x*
*switchport mode access*
*switchport access vlan x*

*vlan x (creates vlan)*
*name <name>*

In a small network with frew VLANs, it is possible to use a separate interface for each VLAN when connecting switches to switches, and switches to routers.
However, when the number of VLANs increases, this is not viable. It will result in wasted interfaces, and often routers won't have enough interfaces for each VLAN.
You can use trunk ports to carry traffic from multiple VLANs over a single interface.

What is a trunk port? It is a switch interface that carries traffic over multiple VLANs.

Switches will 'tag' all frames that they send over a trunk link. This allows the receiving switch to know which VLAN the frame belongs to.
Trunk ports = 'tagged' ports
Access ports = 'untagged' ports

There are two main trunking protocols: ISL (Inter-Switch Link) and IEEE 802.1Q
ISL is an old Cisco proprietary protocol created before the industry standard IEEE 802.1Q
IEEE 802.1Q is an industry standard protocol created by the IEEE (Institute of Electrical and Electronics Engineers).

The 802.1Q tag is inserted between the Source and Type/Length fields of the Ethernet frame.
The tag is 4 bytes in length.
The tag consists of two main fields"
  Tag Protocol Identifier (TPID)
  Tag Control Information (TCI)

The TCI consists of three sub-fields.

The TPID (Tag Protocol Identifier) is 2 bytes in length.
Always set to a value of 0x8100. This indicates that the frame is 802.1Q-tagged.

PCP (Priority Code Point) is 3 bits in length.
Used for Class of Service (CoS), which prioritizes important traffic in congested networks.

DEI (Drop Eligible Indicator) is 1 bit in length.
Used to indicate frames that can be dropped if the network is congested.

VID (VLAN ID) 12 bits in length.
Identifies the VLAN the frame belongs to.
12 bits in length = 4096 total VLANs, range of 0-4095
VLANs 0 and 4095 are reserved and can't be used.
Therefore, the actual range of VLANs is 1 - 4094

The range of VLANs(1-4094) is divided into two sections:
  Normal VLANs: 1 - 1005
  Extended VLANs: 1006-4094
Some older devices cannot use the extended VLAN range, however it's safe to expect that modern switches will support the extended range.

802.1Q has a feature called the native VLAN.
The native VLAN is VLAN 1 by default on all trunk ports, however this can be manually configured on each trunk port.
The switch does not add an 802.1Q tag to frames in the native VLAN.
When a switch receives an untagged frame on a trunk port, it assumes the frame belongs to the native VLAN.

**Trunk configuration**

*switchport trunk encapsulation dot1q* (this is not neccessary for switches that only support 802.1Q)
*switchport mode trunk* (configure the interface as a trunk)
*show interfaces trunk* 
*switchport trunk allowed vlan x* (allows you to add vlans to the allowed list)
*switchport trunk allowed vlan remove x* (allows you to removed allowed vlans from list)

For security purposes, it is best to change the native VLAN to an unused VLAN.
*switchport trunk native vlan x* (change native vlan)

The *show vlan brief* command shows the access ports assigned to each VLAN, NOT the trunk ports that allow each VLAN.
Use the *show interfaces trunk* command instead to confirm trunk ports.

Sub interfaces for a Router on a Stick
*interface gx/x.xx* (subinterface number does not have to match VLAN number, but it is recommended that they do match)
*encapsulation dot1q 10*
*ip address x.x.x.x x.x.x.x*

Router on a Stick is used to route between multiple VLANs using a single interface on the router and switch.
The switch interface is configured as a regular trunk.
The router interface is configured using subinterfaces. You configure the VLAN tag and IP address on each subinterface.
The router will behave as if frames arriving with a certain VLAN tag have arrived on the subinterface configured with that VLAN tag.
The router will tag frames sent out of each subinterface with the VLAN tag configured on the subinterface.

There are two methods of configuring the native VLAN on a router:
use the command *encapsulation dot1q vlan-id native* on the router subinterface
configure the ip address for the native VLAN on the router's physical interface (the *encapsulation dot1q vlan-id* command is not necessary)

A multilayer switch is capable of both switching AND routing.
It is 'Layer 3 aware'.
You can assign IP addresses to its interfaces, like a router.
You can create virtual interfaces for each VLAN, and assign IP addresses to those interfaces.
You can configure routes on it, just like a router.
It can be used for inter-VLAN routing. 

SVIs (Switch Virtual Interfaces) are the virtual interfaces you can assign IP addresses to in a multilayer switch.
Configure each PC to use the SVI (NOT the router) as their gateway address.
To send traffic to different subnets/VLANs, the PCs will send traffic to the switch, and the switch will route the traffic.

*ip routing* - This command enables Layer 3 routing on the switch.
*no switchport* - This command configures the interface as a 'routed port'

*show interfaces status* - shows interfaace status on a switch

creating SVIs:
*interface vlan <vlan-id>*
*ip add <ip-add> <mask>*

SVIs are shutdown by default, so remember to use *no shutdown*

In order for a vlan to be up:
1. the VLAN must exist on the switch
2. The switch must have at least one access port in the VLAN in an up/up state, AND/OR one trunk port that allows the VLAN that is in an up/up state.
3. The VLAN must not be shutdown
4. The SVI must not be shutdown

* DTP and VTP were removed from CCNA exam topics lists. However it is still important to learn them

DTP (Dynamic Trunking Protocol) is a Cisco propreitary protocol that allows Cisco switches to dynamically determine their interface status (access or trunk) without manual configuration.
DTP is enabled by default on all Cisco switch interfaces.
For security purposes, manual configuration is recommended. DTP should be disabled on all switchports.


A switchport in dynamic desirable mode will actively try to form a trunk with other Cisco switches. It will form a trunk it connected to another switchport in the following modes:
*switchport mode trunk*
*switchport mode dynamic desirable*
*switchport mode dynamic auto*

DTP will not form a trunk with a router, PC, etc. The switchport will be in access mode.

On older switches, switchport mode dynamic desirable is the defaul administrative mode.
On new switches, switchport mode dynamic auto is the default administrative mode.
You can disable DTP negotiation on an interface with this command: *switch nonegotiate*
Configuring an access port with switchport mode access also disables DTP negotiation on an interface.
It is recommended that you disable DTP on all switchports and manually configure them as access or trunk ports.

Switches that support both 802.1Q and ISL trunk encapsulations can use DTP to negotiate the encapsulation they will use.
This negotiation is enabled by defualt, as the default trunk encapsulation mode is: switchport trunk encapsulation negotiate.
ISL is favored over 802.1Q, so if both switches support ISL it will be selected.
DTP frames are sent in VLAN1 when using ISL, or in the native VLAN when using 802.1Q.

VTP (VLAN Trunking Protocol) allows you to configure VLANs on a centrla VTP server switch, and other switches (VTP clients) will synchronize their VLAN database to the server.
It is designed for large networks with many VLANs, so that you don't have to configure each VLAN on every switch.
It is rarely used, and it is recommended that you do not use it.
There are three VTP versions: 1,2, and 3.
There are three VTP modes: server, client, and transparent.

VTP servers:
  can add/modify/delete VLANs
  store the VLAN database in non-volatile RAM
  will increase the revision number every time a VLAN is added/modified/deleted.
  will advertise the latest version of the VLAN database on trunk interfaces, and the VTP clients will synchronize their VLAN database to it.
  VTP servers also function as VTP clients
  Therefore, a VTP server will synchronize to another VTP server with a higher revision number.

VTP clients:
  Cannot add/modify/delete VLANs
  Do no store the VLAN database in NVRAM (in VTPv3, they do)
  Will synchronize their VLAN database to the server with the highest revision number in their VTP domain.
  will advertise their VLAN database, and forward VTP adertisements to other clients over their trunk ports.

*show vtp status*

If a switch with no VTP domain (domain NULL) receives a VTP advertisement with a VTP domain name, it will automatically join that VTP domain.
If a switch receives a VTP advertisement in the same VTP domain with a higher revision number, it will update it's VLAN database to match.

One danger of VTP: If you connect an old switch with a higher revision number to your network (and the VTP domain name matches), all
switches in the domain will sync their VLAN database to that switch.

VTP Transparent mode:
  Does no participate in the VTP dmaon (does not sync its VLAN database).
  Maintains its own VLAN database in NVRAM. It can add/modify/delete VLANs, but they won't be advertised to other switches.
  Will forward VTP advertisements that are in the same domain as it.
  Resets VTP revision number to 0.

*vtp mode client* (turns switch into VTP Client)
*vtp mode transparent* (turns switch into VTP Transparent)

Cisco switches operate in VTP server mode by default.

Redundancy is an essential part of network design.

Modern networks are expected to run 24/7/365. Even a short downtime can be disastrous for a business.
If one network component fials, you must ensure that other components will take over with little or no downtime.
As much as possible, you must implement redundancy at every possible point in the network.

Most PCs only have a single network interface card (NIC), so they can only be plugged into a single siwtch. 
However, important servers typically have multiple NICs, so they can be plugged into multiple switches for redundancy.

The Ethernet header doesn't have a TTL field. These broadcast frames will loop around the network indefinitely.
If enough of these looped broadcasts accumulate in the network, the network will be too congested for legitimate traffic to use the network.
This is called a broadcast storm.

Network congestion isn't the only problem. Eeach time a frame arrives on a switchport, the switch uses the source MAC address field to 'learn' the MAC address
and update its MAC address table. When frames with the same source MAC address repeatedly arrive on different interfaces, the switch is continuously updating
the interface in its MAC address table. This is known as MAC Address Flapping.

Classic Spanning Tree Protocol is IEEE 802.1D
Switches from ALL vendors run STP by default.
STP prevents Layer 2 loops by placing redundant ports in a blocking state, essentially disabling the interface.
These interfaces act as backups that can enter a frowarding state if an active (=currently forwarding) interface fails.
Interfaces in a forwarding state behave normally. They send and receive all normal traffic.
Interfaces in a blocking state only send or receieve STP messages (called BPDUs = Bridge Protocol Data Units).

Spanning Tree Protocol still uses the term 'bridge'. However, when we use the term 'bridge', we really mean 'switch'. Bridges are not used in modern networks.

By selecting which ports are forwarding and which ports are block, STP creates a single path to/from each point in the network. This prevents Layer 2 loops.
There is a set process that STP uses to determine which ports should be forwarding and which should be blocking.
STP-enabled switches send/receieve Hello BPDUs out of all interfaces, the default timer is 2 seconds (the switch will send a Hello BPDU out of every interface, once every 2 seconds).
If a switch receives a Hello BPDU on an interface, it knows that interface is connected to another switch (routers, PCs, etc. do not use STP, so they do not send Hello BPDUs).

Swiches use one field in the STP BPDU, the Bridge ID field, to elect a root bridge for the network.
The switch with the lowest Bridge ID becomes the root bridge.
ALL ports on the root bridge are put in a forwarding state, and other switches in the topology must have a path to reach the root bridge.

Bridge ID
Bridge Priority (16 bits) - the default bridge priority is 32768 on all switches, so by default the MAC address is used as the tie-breaker.
MAC Address (48 bits)

Bridge Priority field has been updated to two fields:
Bridge Priority (4 bits)
Extended System ID (=VLAN ID) (12 bits)
Cisco switches use a version of STP called PVST (Per-VLAN Spanning Tree). PVST runs a seperate STP 'instance' in each VLAN, so in each VLAN different interfaces can be forwarding/blocking.
In the default VLAN of 1, the default bridge priority is actually 32769 (32768 + 1)
The STP bridge priority can only be changed in units of 4096.

All interfaces on the root bridge are designated ports. Designated ports are in a forwarding state.

When a switch is powered on, it assumes it is the root bridge.
It will only give up its position if it receives a 'superior' BPDU (lower bridge ID).
Once the topology has converged and all switches agree on the root bridge, only the root bridge sends BPDUs
Other switches in the network will forward these BPDUs, but will not generate their own original BPDUs.

1) The switch with the lowest bridge ID is elected as the root bridge. All ports on the root bridge are designated ports.
2) Each remaining switch will select ONE of its interfaces to be its root port. The interface with the lowest root cost will be the root port. Root ports are also in a forwarding state.
3) Each remainting collision domain will select ONE interface to be designated port. The other port in the collision domain will be non-designated.

Speed STP Cost
10 Mbps 100
100 Mbps 19
1 Gbps 4
10 Gbps 2

The ports connected to another switche's root port MUST be designated. Because the root port is the switch's path to the root bridge, another switch must not block it.
Root port selection:
1) Lowest root cost
2) Lowest neighbor bridge ID
3) Lowest neighbor port ID

STP Port ID = port priority (default 128) + port number

Every collision domian has a single STP designated port.

1) The switch with the lowest root cost will make its port designated.
2) If the root cost is the same, the switch with the lowest bridge ID will make its port designated.
3) The other switch will make its port non-designated (blocking)

*show spanning-tree*

Root/Designated ports remain stable in a Forwarding state.
Non-Designated ports remain stable in a Blocking state.
Listening and Learning are transitional states which are passed through when an interface is activated, or when a Blocking port must transition to a Forwarding state
due to a chnage in the network topology.

After the Blocking state, interfaces with the Designated or Root role enter the Listening state.
The Listening state is 15 seconds long by deafult. This is determined by the Forward delay timer.
An interface in the Listening state ONLY forwards/receives STP BPDUs.

After the Listening state, a Designated or Root port will enter the Learning state.
15 seconds long by deafult. Determined by the Forward delay timer.
An interface in the Learning state ONLY sends/receives STP BPDUs and learns MAC addresses from regular traffic that arrives on the interface.

Hello Timer - How often the root bridge sends hello BPDUs (2 sec)
Forward delaay - How long the switch will stay in the Listening and Learning states (15 sec)
Max Age - How long an interface will wait to change the STP topology after ceasing to receive Hello BDPUs. (20 sec)

IF another BPDU is received before the max age timer counts donw to 0, the time will reset to 20 seconds and no changes will occur.
If another BPDU is not received, the max age timer counts down to 0 and the switch will reevaluate its STP choices, including root bridge, and local root, designated, and non-designated ports.
If a non-designated port is selected to become a designated or root port, it will transition from the blocking state to the listening state (15 sec), learning state (15 sec), and
then finally the forwarding state. So, it can take a total of 50 seconds for a blocking interface to transition to forwarding.
These timers and transitional states are to make sure that loops aren't accidentally created by an interface moving to forwarding that too soon.

A forwarding interface can move directly to a blocking state (there is no worry about creating a loop by blocking an interface).
A blocking interface cannot move directly to forwarding state. It must go through the listening and learning states.

Ethernet header PVST+ uses MAC address (01: 00 : 0c : cc : cc :cd)
PVST = Only ISL trunk encapsulation
PVST+ = Supports 802.1Q
Regular STP uses a destination MAC address of 0180.c200.0000

Portfast allows a port to move immediately to the Forwarding state, bypassing Listening and Learning.
If used, it must be enabled only on ports connected to end hosts.
If enabled on a port connected to anohter switch it could cause a Layer 2 loop.

*spanning-tree portfast*
*spanning-tree portfast default* (This enables portfast on all access ports (not trunk ports)).

If an interface with BPDU Guard enabled receives a BPDU from another switch, the interface will be shut down to prevent a loop from forming.
*spanning-tree bpduguard enable*
*spanning-tree portfast bpduguard default* (This enables BPDU Guard on all Portfast-enabled interfaces)

Root guard - If you enabled root guard on an interface, even if it receives a superior BPDU on that interface, the switch will not accept
the new switch as the root bridge. The interface will be disabled.

Loop Guard - If you enable loop guard on an interface, even if the interface stops receiving BPDUs, it will not start forwarding. The interface will be disabled.

*spanning-tree vlan <vlan-#> root primary* configure switch to be root bridge by setting STP priority to 24576 or less than whatever the lowest is
*spanning-tree vlan <vlan-#> root secondary* sets the STP priority to 28672

Spanning Tree Protocol (802.1D)
- The original STP
- All VLANs share one STP instance
- Therefore, cannot load balance

Rapid Spanning Tree Protocol (802.1w)
- Much fast at converging/adapting to network changes than 802.1D
- All VLANs share one STP instance
- Therefore, cannot load balance

Multiple Spanning Tree Protocol (802.1s)
- Uses modified RSTP mechanics
- Can group multiple VLANs into different instances to perform load balancing

Per-VLAN Spanning Tree Plus (PVST+)
- Cisco's upgrade to 802.1D
- Each VLAN has its own STP instance
- Can load balance by blocking different ports in each VLAN

Rapid Per-VLAN Spanning Tree Plus (Rapid PVST+)
- Cisco's upgrade to 802.1w
- Each VLAN has its own STP instance
- Can load balance by blocking different ports in each VLAN.

Cisco summary of RSTP: "RSTP is not a timer-based spanning tree algorithm like 802.1D. Therefore, RSTP offers an improvement over the 30 seconds or more that
802.1D takes to move a link to forwarding. The heart of the protocol is a new bridge-bridge handshake mechanism, which allows ports to move directly to forwarding."

Similiarties between STP and RSTP:
- RSTP serves the same purpose as STP, blocking specific ports to prevent Layer 2 loops.
- RSTP elects a root bridge with the same rules as STP.
- RSTP elects root ports with the same rules as STP.
- RSTP elects designated ports with the same rules as STP.

Speed  STP Cost  RSTP Cost
10 Mbps 100   2,000,000
100 Mbps 19   200,000
1  Gbps 4     20,000
10 Gbps 2     2,000
100 Gbps 1    200
1 Tbps   1    20

Rapid Spanning Tree Port States
Discarding
Learning
Forwarding

The non-designated port role is split into two separate roles in RSTP:

The RSTP alernate port role is a discarding port that receives a superior BPDU from another switch.
This is the same as what you've learned about blocking ports in classic STP.
Functions as a backup to the root port.
If the root port fails, the switch can immediately move its best alternate port to forwarding.
This immediate move to forwarding state functions like a classic STP optional feature called UplinnkFast.
Because it is built into RSTP, you do not need to activate UplinkFast when using RSTP/Rapid PVST+.

Onemore STP optional feature that was built into RSTP is BackboneFast.
BackboneFast allows SW3 to expire the made age timers on its interface and rapidly forward the superior BPDUs to SW2.
This functionality is built into RSTP, so it does not need to be configured.

The RSTP backup port role is a discarding port that receives a superior BPDU from another interface on the same switch.
This only happens when two interfaces are connecetd to the same collision domain (via a hub)
Hubs are not used in modern networks, so you will probably not encounter an RSTP backup port.
Functions as a backup for a designated port.
The interface with the lowest port ID will be selected as the designated port, and the other will be the backup port.

BPDU Type - 0x02  RSTP
0x00 - STP

In classic STP, only the root bridge originated BPDUs, and other switches just forwarded the BPDUs they received.
In rapid STP, ALL switches originate and send their own BPDUs from their designated ports. 

Switches 'age' the BPDU infromation much more quickly. In classic STP, a switch waits 10 hello intervals (20 seconds). In rapid STP, a switch considers 
a neighbor lost if it misses 3 BPDUs (6 seconds). It will then 'flush' all MAC addresses learned on that interface. 

RSTP Link Types
RSTP distinguishes between three different 'link types'.
Edge: a port that is connected to an end host. Moves directly to forwarding, without negotiation.
Point-to-point: a direct connection between two switches.
Shared: a connection to a hub. Must operate in half-duplex mode.

Edge ports are connecetd to end hosts.
Because there is no risk of creating a loop, they can move straight to the forwrding state without the negoatiation process.
They function like a classic STP port with PortFast enabled.

Point-to-point ports connect dirently to another switch.
They fucntion in full-duplex.
You don't need to configure the interface as point-to-point.

Shared ports connect to anothe rswitch (or siwtches) via a hub.
They function in half-duplex.
You don't need to configure the interface as shared.

*spanning-tree link-type point-to-point* -> point to point links
*spanning-tree portfast* -> end hosts

ASW = Access Layer Switch, a switch that end hosts connect to.
DSW = Distribution layer switch, a switch that access layer switches connect to.

When the bandsidth of the interfaces connected to end hosts is greater than the bandwidth of the connection to the distribution switch(es), this is called oversubscription.
Some oversubsciprtion is acceptable, but too much will cause congestion.

If you connect two switches together with multiple links, all except one will be disabled by spanning tree.
If all of ASW1's interfaces were forwarding, Layer 2 loops would form between ASW1 and DSW1, leading to broadcast storms.
Other links will be unused unless the active link fails. In that case, one of the inactive links will start forwarding.

EtherChannel groups multiple interfaces together to act as a single interface.
STP will treat this group as a single interface.
Traffic using the EtherChannel will be load balanced among the physical interfaces in the group. An algorithm is used to determine which traffic will use which physical interface.
Some other names for an EtherChannel are:
Port Channel
LAG (Link Aggregation Group)

EtherChannel load balances based on 'flows'
A flow is a communication between two nodes in the network.
Frames in the same flow will be forwarded using the same physical interface.
If frames in the same flow were forwarded using different physical interfaces, some frames may arrive at the destination out of order, which can cause problems.

You can change the inputs used in the interface selection calculation.
Inputs that can be used: Source MAC, Destination MAC, Source AND Destination MAC, Source IP, Destination IP, Source and Destination IP

*show etherchannel load-balance* (check current load-balance config)
*port-channel load-balance <method>* (change load-balance method)

Three methods of EtherChannel configuration on Cisco switches:
PAgP (Port Aggregation Protocol)
-> Cisco propreitary protocol
-> Dynamicallly negotiates the creation/maintenance of the EtherChannel (like DTP does for trunks)
LACP (Link Aggregation Control Protocol)
-> Industry standard protocol (IEEE 802.3ad)
-> Dynamically negotiates the creation/maintenance of the EtherChannel (like DTP does for trunks)
Static EtherChannel
-> A protocol isn't used to determine if an EtherChannel should be formed.
-> Interfaces are statically configured to form an EtherChannel

Up to 8 interfaces can be formed into a single EtherChannel (LACP allows up to 16 but only 8 will be active, the other
8 will be in standby mode, waiting for an active interface to fail.)

*channel-group <#> mode <mode>*

PGAP:
auto + auto = no EtherChannel
desirable + auto = EtherChannel
desirable + desirable = EtherChannel

LACP:
passive + passive = no EtherChannel
active + passive = EtherChannel
active + active = EtherChannel

On mode only works with on mode (on + desirable or on + active will not work)

*channel-protocol ?* (manually configures the EtherChannel protocol)

*interface port-channel <#>*

Member interfaces must have matching configurations.
-> Same duplex
-> Same speed
-> Same switchport mode
-> Same allowed VLANs/native VLAN
If an interface's configurations do not match the others, it will be excluded from the channel

*show etherchannel summary*
*show etherchannel port-channel*

Configuring Layer 3 EtherChannel:
*no switchport*
*channel-group 1 mode active*

Network route - a route to a network/subnet (mask length </32)
Host route - a route to a specific host (/32 mask)

Routers can use dynamic routing protocols to advertise information about the routes they know to other routers.
The form 'adjacencies'/'neighbor relationships'/'neighborships' with adjacent routers to exchange this information.
If multiple routes to a destination are learned, the router determins which route is superior and adds it to the routing table.
It uses the 'metric' of the route to decide which is superior (lower metric = superior)


Dynamic routing protocls can be divided into two main categories:
IGP (interior Gateway Protcol)
EGP (Exterior Gateway Protocol)

IGP = used to share routes within a single autonomous system (AS), which is a single organization
EGP = used to share routes between different autonomous systems

Algorithm type
EGP - Path Vector -> Border Gateway Procotol (BGP)
IGP - Distance Vector -> Routing Information Protocol (RIP), Enhanced Interior Gateway Routing Protocol (EIGRP)
    - Link State -> Open Shortest Path First (OSPF), Intermediate System to Intermediate System (IS-IS)

Distance vector protocols were invented before link state protocls.
Early examples are RIPv1 and Cisco's proprietary protocol IGRP (which was update to EIGRP)
Distance vector protocols operate by sending the following to their directly connected neighbors:
-> their known destination networks
-> their metric to reach their known destination networks
This method of sharing route information is often called 'routing by rumor'
This is because the router doesn't know about the network beyond its neighbors. It only knows the information that its neighbors tell it.
Called 'distance vector' because the routers only learn the 'distance' (metric) and 'vector' (direction, the next-hop router) of each route

When using a link state routing protocol, every router creates a 'connectivity map' of the network
To allow this, each router advertises information about its interfaces (connected networks) to its neighbors.
These advertisements are passed along to other routers, until all routers in the network develop the same map of the network.
Each router independentlky uses this map to calculate the best routes to each desitnation.
Link state protocols use more resources (CPU) on the router, because more information is shared.
However, link state protocls tend to be faster in reacting to changes in the network than distance vector protocols.

A router's route table contains the best route to each destination network it knows about.
If a router using a dynamic routing protocl learns two different routes to the same destination, how does it determine which is 'best'?
It uses the metric value of the routes to determine which is best. A lower metric = better.
Each routing protocl uses a different metric to determine which route is the best.

If a router learns two (or more) routes via the same routing protocl to the same destination (same network address, same subnet mask) with the same
metric, both will be added to the routing table. Traffic will be load-balanced over both routes.

ECMP (Equal Cost Multi-Path)

Static routes will always have a metric of 0 and AD of 1.

![image](https://github.com/fernskers/CCNA/assets/57144399/369307ca-5512-41a3-b367-28f53be030ac)

In most cases a company will only use a single IGP - usually OSPF or EIGRP
However, in some rare cases they might use two. For example, if two companies connect their networks to share information,
two different routing prtocols might be in use.
Metric is used to compare routes learned via the same routing protocol.
Different routing protocols use totally different metrics, so they cannot be compared.
For example, an OSPF route to 192.168.4.0/24 might have a metric of 30, while an EIGRP route to the same destination  might have a metric of 33280.
The administrative distance (AD) is used to determine which routing protocl is preferred.
A lower AD is preferred, and indicates that the routing protocol is considered more 'trustworthy' (more likelly to select good routes)

![image](https://github.com/fernskers/CCNA/assets/57144399/26790c97-90ff-4ff2-980f-d14d9fd50667)
![image](https://github.com/fernskers/CCNA/assets/57144399/74ad340b-a939-4f0c-9236-149da5c08991)

By changing the AD of a static route, you can make it less preferred than routes learned by a dynamic routing protocol to the same destination.
This is called a 'floating static route'
The route will be inactive (not in the routing table) unless the route learned by the dynamic routing protocl is removed.

Routing Information Protocol (industry standard) - RIP
Distance vector IGP (uses routing-by-rumor logic to learn/share routes)
Uses hop count as its metric. One router = one hop (bandwidth is irrelevant!)
The maximum hop count is 15 ( anything more than that is considered unreachable)
Has three versions:
  RIPv1 and RIPv2, used for IPv4
  RIPng (RIP Next Generation), used for IPv6

Uses two message types:
  Request: To ask RIP-enabled neighbor routers to send their routing table
  Response: To send the local router's routing table to enighboring routers

By default, RIP-enable routers will share their routing table every 30 seconds

RIPv1: 
- only advertises classful addresses (Class A, Class B, Class C)
- doesn't support VLSM, CIDR
- doesn't include subnet mask information in advertisements (Response messages)
    10.1.10/24 will become 10.0.0.0 (Class A address, so assumed to be /8)
    172.16.192.0/18 will become 172.16.0.0 (Class B address, so assumed to be /16)
    192.168.1.4/30 will become 192.168.1.0 (Class C address, so assumed to be /24)
- message are broadcast to 255.255.255.255

RIPv2:
- supports VLSM, CIDR
- includes subnet mask information in advertisements
- messages are multicast to 224.0.0.9

Broadcast messages are delivered to all devices on the local network.
Multicast messages are delivered only to devices that have joined that specific multicast group

configuring rip:
*router rip*
*version 2* (preferable version of RIP)
*no auto-summary* (converts networks routers advertise to classful networks)
*network x.x.x.x* (network network command is classful, it will convert to classful networks)

The **network** command tells the router to:
  look for interfaces with an IP address that is in the specified range
  active RIP on the interfaces that fall in the range
  form adjacencies with connected RIP neighbors
  advertise the network prefix of the interface (NOT the prefix in the network command)
The OSPF and EIGRP network commands operate in the same way.

*passive-interface <interface>* (tells the router to stop sending RIP advertisements out of the specified interface)
*default-information originate* (share deafult routes with neighbors)
*show ip protocols*

EIGRP (Enhanced Interior Gateway Routing Protocol)
- Was Cisco priopretary, but Cisco has now published it openly so other vendors can implemnt it on their equipment
- Considered an 'advanced' / 'hybrid' distance vector routing protocol
- Much faster than RIP in reacting to changes in the network
- Does not have the 15 'hop-count' limit of RIP
- Sends messages using multicast address 224.0.0.10
- Is the only IGP that can perform unequal-cost load-balancing (by default it performs ECMP load-balancing over 4 paths like RIP)

EIGRP configuration:
*router eigrp <AS #>* The AS number must match between routers, or they will not form an adjacency and share route information.
*no auto-summary* auto summary will advertise classful networks, disable this bs
*passive-interface <interface>*
*network x.x.x.x* The network command will assume a classful address if you don't specify the mask
*network x.x.x.x <wildcard-mask>*

EIGRP uses a wildcard mask instead of a regular subnet mask.
A wildcard mask is basically an 'inverted' subnet mask.
All 1s in the subnet mask are 0 in the equivalent wildcard mask. All 0s in the subnet mask are 1 in the equivalent wildcard mask.

'0' in the wildcard mask = must match
'1' in the wildcard mask = don't have to match

Router-ID order of priority:
1) Manual configuration
2) Highest IP address on a loopback interface
3) Highest IP address on a physical interface

*eigrp router-id <router-id>* (configure eigrp router id)

By default, EIGRP uses bandswidth and delay to calculate metric
((K1 * bandwidth + (K2 * bandwidth) / (256 - load) + K3 * delay ) * (K5 / (reliability + K4))) * 256
The default 'K' values are K1 = 1, K2 = 0, K3 = 1, K4 = 0, K5 = 0
You can simplify the formula like this: metric = bandwidth of the slowest link + the delay of all links

Feasible Distance - This router's metric value to the route's destination
Reported Distance (aka Advertised Distance) = The neighbor's metric value to the route's destination.
Successor = the route with the lowest metric to the destination (the best route)
Feasible Successor = an alternate route to the destination (not the best route) which meets the feasibility condition
Feasibility condition: A route is considered a feasible successor if it's reported distance is lower than the successor route's feasible distance.

Variance 1 = only ECMP load-balancing will be performed
*variance <#>* Metric variance Multiplier
Variance 2 = feasible successor routes with an FD up to 2x the successor route's FD can be used to load-balance

EIGRP will only perform unequal-cost load-balancing over feasible successor routes. If a route doesn't meet the feasibility requirement, it will NEVER be selected for load-balancing, regardless of the variance.

When using a link state routing protocol, every router creates a 'connectivity' map of the network.
To allow this, each router advertises information about its interfaces (connceted networks) to its neighbors. These advertisements are passed along to other routers, until all routers
in the network develop the same map of the network.
Each router independently uses this map to calculate the best routes to each destination.
Link state protocls use more resources (CPU) on the router, because more information is shared.
However, link state protocols tend to be faster in reacting to changes in the network than distance vector protocols.

Stands for Open Shortest Path First
Uses the Shortest Path First algorithm of Dutch computer scientisht Edsger Dijkstra.
Three versions:
OSPFv1 (1989): OLD, not in use anymore
OSPFv2 (1998): Used for IPv4
OSPFv3 (2008): Used for IPv6 (can also be used for IPv4, but usually v2 is used)
Routers store information about the network in LSAs (Link State Advertisements), which are organized in a structure called the LSDB (Link State Database).
Routers will flood LSAs until all routers in the OSPF area develop the same map of the network (LSDB).

The LSA is flooded throughout the network until all routers have received it.
The results in all routers sharing the same LSDB.
Each LSA has an aging timer (30 min by default). .The LSA will be flooded again after the timer expires.

In OSPF, there are three main steps in the process of sharing LSAs and determining the best route to each destination in the network.
1) Become neighbors with other routers connected to the same segment
2) Exchange LSAs with neighbor routers
3) Calculate the best routes to each destination, and insert them into the routing table.

OSPF uses areas to divide up the network
Small networks an be single-area without anynegative effects on performance
In larger networks, a single-area design can have negative effects:
 - the SPF algorithm takes more time to calculate routes
 - the SPF algorithm requires exponentially more processing power on the routers
 - the larger LSDB takes up more memory on the routers
 - any small change in the network causes every router to flood LSAs and run the SPF algorithm again

By dividing a large OSPF network into serveral smaller areas, you can avoid the above negative effects.

An area is a set of routers and links that share the same LSDB
The backbone area (area 0) is an area that all other areas must connect to.
Routers with all interfaces in the same area are called internal routers.
Routers with interfaces in multiple areas are called area border routers (ABRs)
Routers connceted to the backbone area (area 0) are called backbone routers.
An intra-area route is a route to a destination inside the same OSPF area.
An inter-area route is a route to a destination in a different OSPF area.

ABRs maintain a separate LSDB for each area they are connected to. It is recommended that you connect an ABR to a maximum of 2 areas.
Connecting an ABR to 3+ areas can overburden the router.

OSPF areas should be contiguous. (areas should be connected, not divided)
All OSPF areas must have at least one ABR connceted to the backbone area.
OSPF interfaces in the same subnet must be in the same area.

OSPF config:
*router ospf <process-ID>*
*network <ip-address> <wildcard-mask> area <area-ID>*

Note: OSPF process ID is locally significant. Routers with different process IDs can become OSPF neighbors
The network command tells OSPF to...
look for any interfaces with an IP address contained in the range specified in the network command
Activate OSPF on the interface in the specified area
The router will then try to become OSPF neighbors with other OSPF-activated neighbor routers.

*passive-interface <interface>* (tells router to stop sending OSPF 'hello' messages out of the interface)
However, the router will continue to send LSAs informing it's neighbors about the subnet configured on the interface.
You should always use this command on interfaces which don't have any OSPF neighbors
*default-information originate* (create a new LSA and flood it, declare default route on this interface)
*show ip protocols*

Router ID order of priority:
1) Manual configuration
2) Highest IP address on a loopback interface
3) Highest IP address on a physical interface

*clear ip ospf process* (resets OSPF router)
*router-id <router-id>*

An autonomous system boundary router (ASBR) is an OSPF router that connects the OSPF network to an external network.
R1 is connected to the Internet. By using the default-information originate command, R1 becomes an ASBR.
*maximum-paths <number>* change maximum paths

OSPF's metric is called cost
It is automatically calculated based on the bandswidth (speed) of the interface
It is calculated by dividing a reference bandwidth value by the interface's bandwidth 
The default reference bandwidth is 100 mbps
  Reference: 100 mbps / Interface: 10 mbps = cost of 10
  Reference: 100 mbps / Interface: 100 mbps = cost of 1
  Reference: 100 mbps / Interface: 1000 mbps = cost of 1

All values less than 1 will be converted to 1.
Therefore Fast Ethernet, Gigabit Ethernet, 10Gig Ethernet, etc. are equal and all have a cost of 1 by default.

You can change the reference bandwidth with this command:
*auto-cost reference-bandswidth <megabits-per-second>*
You should configure a reference bandwidth greater than the fastest links in your network.
You should configure the same reference bandwidth on all OSPF routers in the network.

The OSPF cost to a destination is the toal cost of the outgoing interfaces
Loopback interfaces have a cost of 1

*ip ospf cost <cost>* manually configure cost

One more option to change the OSPF cost of an interface is to change the bandwidth of the interface with the bandwidth command.
Although the bandwidth matches the interface speed by default, changing the interface bandwidth doesn't actually change the speed at which the interface operates.
The bandsidth is just a value that is used to calculate OSPF cost, EIGRP metric, etc.
To change the speed at which the interface operates, use the speed command.
Because the bandwidth value is used in other calculations, it is not recommended to change this value to alter the interface's OSPF cost.
It is recommended that you change the reference bandwidth, and then use the *ip ospf cost* command to change the cost of individual interfaces if you want.

Three ways to modify the OSPF cost:
1) Change the reference bandwidth
2) Manual configuration
3) Change the interface bandwidth *bandwidth <kilobits-per-seconds>*

*show ip ospf interface brief*

Making sure that routers successfullly become OSPF neighbors is the main task in configuring and troublshooting OSPF
Once routers become neighbors, they automatically do the work of sharing network information, calculating routes, etc.
When OSPF is activated on an interface, the router starts sending OSPF hello messages out of the interface at regular intervals (determined by the hello timer). These are used to introduce
the router to potential OSPF neighbors.
The default hello timer is 10 seconds on an Ethernet connection.
Hello message are multicast to 224.0.0.5 (multicast address for all OSPF routers)
OSPF messages are encapsulated in an IP header, with a value of 89 in the Protocol field

OSPF states - Down, Init, 2-way, Exstart, Exchange, Loading, Full

In the Loading state, routers send Link State Request (LSR) messgaes to request that their neighbors to request that their neighbors send them any LSAs they don't have.
LSAs are sent in Link State Update (LSU) messages.
The routers send LSAck messages to aknowledge that they received the LSAs.

In the Full state, the routers have a full OSPF adjacency and identical LSDBs
They continue to send and listen for Hello packets (every 10 seconds by default) to maintain the neighbor adjacency.
Every time a Hello packet is reeived, the 'Dead' timer (40 seconds by default) is reset.
If the Dead timer counts down to 0 and no Hello message is received, the neighbor is removed
The routers will continue to share LSAs as the network changes to make sure each router has a complete and accurate map of the network (LSDB)

![image](https://github.com/fernskers/CCNA/assets/57144399/a7a60b39-6678-41b3-847d-91ed85da9d3c)

*show ip ospf neighbor*

*ip ospf <process-id> area <area>* activate OSPF directly on an interface
*passive-interface default* configure ALL interfaces as OSPF passive interfaces
*no passive-interface <int-id>* configure specific interfaces as active

A loopback interface is a virtual interface in the router
It is always up/up (unless manually shut down)
It is not dependent on a physical interface
So, it provides a consistent IP address that can be used to reach/identify the router.

The OSPF 'network type' refers to the type of connection between OSPF neighbors
There are three main OSPF network types:
  Broadcast - enabled by default on Ethernet and FDDI (Fiber Distributed Data Interfaces) interfaces
  Point-to-point - enabled by Default on PPP (Point-to-Point Protocol) and HDLC (High-Level Data Link Control) interfaces
  Non-broadcast - enabled by default on Frame Relay and X.25 interfaces

Router dynamically discover neighbors by sending/listening for OSPF Hello messages using multicast address 224.0.0.5
A DR (designated router) and BDR (backup designated router) must be elected on each subnet (only DR if there are no OSPF neighbors, ie. R1's G1/0 interface)
Routers which aren't the DR or BDR become a DROther

The DR/BDR election order of priority:
1. HIghest OSPF interface priority
2. Highest OSPF Router ID

'First place' becomes the DR for the subnet, 'second place' becomes the BDR
The default OSPF interface priority is 1 on all interfaces

*ip ospf priority <priority>* set OSPF interface priority

Note: If you set the OSPF interface priority to 0, the router CANNOT be the DR/BDR for the subnet
The DR/BDR election is 'non-preemptive'. Once the DR/BDR are selected they will keep their role until OSPF is reset

When the DR goes down, the BDR becomes the new DR. Then an election is held for the next BDR.
DROrthers will only omve to the FULL state with the DR and BDR. The neighbor state with other DROthers will be 2-way.

In the broadcast network type, routers will only form a full OSPF adjacency with the DR and BDR of the segment.
Therefore, routers only exchange LSAs with the DR and BDR. DROthers will not exchange LSAs with each other.
All routers will still have the same LSDB, but this reduces the amount of LSAs flooding the network.
Messages to the DR/BDR are multicast using address 224.0.0.6
The DR and BDR will form a FULL adjacency with ALL routers in the subnet. DROthers will form a FULL adjacency only with the DR/BDR.

Point-to-Point Network Type:
Enabled on serial interfaces using the PPP or HDLC encapsulations by default.
Routers dynamically discover neighbors by sending/listening for OSPF Hello messages using multicast address 224.0.0.5
A DR and BDR are not elected
These encapsulations are used for 'point-to-point' connections
Therefore there is no point in electing a DR and BDR
The two routers will form a Full adjacency with each other

Serial Interfaces:
One side of a serial connection functions as DCE (Data Communications Equipment)
The other side functions as DTE (Data Terminal Equipment)
The DCE side needs to specify the clock rate (speed) of the connection
Ethernet interfaces use the speed command to configure the interface's operating speed. Serial interfaces use the clock rate command.
The default encapsulation si HDLC, *encapsulation ppp* change to ppp encapsulation
One side is DCE, other is DTE

If two routers are directly connected with an Ethernet link, there is no need for a DR/BDR. You can configure the point-to-point network type in this case.

![image](https://github.com/fernskers/CCNA/assets/57144399/51b39090-a801-42a5-8458-6a043bb881fd)

OSPF Neighbor Requirements
1) Area number must match
2) Interfaces must be in the same subnet
3) OSPF process must not be shutdown
4) OSPF Router ID's must be unique
5) Hello and Dead timers must match
6) Authentication settings must match
7) IP MTU settings must match
8) OSPF Network Type must match

LSA Types
Type 1 (Router LSA)
- Every OSPF router generates this type of LSA
- It identifies the router using its router ID
- It also lists networks attached to the router's OSPF-activated interfaces

Type 2 (Network LSA)
- Generated by the DR of each 'multi-access' network
- Lists the routers which are attached to the multi-access network

Type 5 (AS-External LSA)
- Generated by ASBRs to describe routes to destinations outside of the AS (OSPF domain)

A first hop redundancy protocol (FHRP) is a computer networking protocol which is designed to protect the deafult gateway
used on a subnetwork by allowing two or more routers to provide backup for that address; in the event of failure of an active router, the backup router will take over the address, usually within a few seconds.

Gratuitoius ARP: ARP replies sent without being requested.

FHRPS are 'non-preemptive'. The current active router will not automatically give up its role, even if the former active router returns.

First Hop Redundancy Protocols
A virtual IP is configured on the two routers, and a virtual MAC is generated for the virtual IP (each FHRP uses a different format for the virutal MAC)
An active router and a standby router are elected (different FHRPs use different terms)
End hosts in the network are configured to use the virtual IP as their default gateway
The active router replies to ARP requests using the virtual MAC address, so traffic destined for other networks will be send to it.
If the active router fails, the standby becomes the next active router. The new active router will send gratuitous ARP messages so that switches will update their MAC address tables. It now functions as the default gateway.
If the old active router comes back online, by default it won't take back its role as the active router. It will become the stanby router.
You can configure 'preempriton', so that the old active router does take back its old role.

HSRP (Hot Standby Router Protocol)
Cisco proprietary.
An active and standby router are elected.
There are two versions: version 1 and version 2
Version 2 adds IPv6 support and increases the number of groups that can be configured.
Multicast IPv4 address: v1 = 224.0.0.2, v2 = 224.0.0.102
Virtual MAC address: v1 = 0000.0c07.acXX (XX = HSRP  group number), v2 = 0000.0c9f.fXXX (XXX = HSRP group number)
In a situation with multiple subnets/VLANs, you can configure a different active router in each subnet/VLAN to load balance.

VRRP (Virtual Router Redundancy Protocol)
Open Standard.
A master and backup router are elected.
Multicast IPv4 address: 224.0.0.18
Virtual MAC address: 0000.5e00.01XX (XX = VRRP group number)
In a situation with multiple subnets/VLANs, you can configure a different master router in each subnet/VLAN to load balance.

GLBP (Gateway Load Balancing Protocol)
Cisco proprietary
Load balances among multiple routers with in a single subnet
An AVG (Active Virtual Gateway) is elected.
Up to four AVFs (Acitve Virtual Forwarders) are assigned by the AVG (the AVG itself can be an AVF, too)
Each AVF acts as the default gateway for a portion of the hosts in the subnet.
Multicast IPv4 address: 224.0.0.102
Virtual MAC address: 0007.b400.XXYY (XX = GLBP group number, YY = AVF number)

![image](https://github.com/fernskers/CCNA/assets/57144399/f79db62e-c150-430f-90f4-c071efd406d4)

Configuring HSRP:
*standby version 2* (change to HSRP version 2)
*standby <group-number> ip <VIRTUAL-ip-address>* (establish group number)
*standby 1 priority <priority-value>* The active router is determind in this order: 1 Highest priority (100 default) 2 - Highest IP address
*standby 1 preempt* Preempt causes the router to take the role of active rotuer, even if another router already has the role

Both routers have to use same version of HSRP

Function of Layer 4 (Transport Layer)
- Provides transparent transfer of data between end hosts.
- Provides (or doesn't provide various services to applications:
  -> reliable data transfer
  -> error recovery
  -> data sequencing
  -> flow control
- Provides Layer 4 addressing (port numbers).
  -> Identify the Application Layer protocol
  -> Provides session multiplexing
  -> The following ranges have been designated by IANA (Internet Assigned Numbers Authority)
  Well-known port number: 0-1023
  Registered port number: 1024 - 49151
  Ephemeral/private/dynamic port number: 49152-65535

TCP (Transmission Control Protocol) is connection-oritented.
- Before actually sending data to the destination host, the two hosts communicate to establish a connection. Once the connection is established, the data exchange begins.

TCP provides reliable communication
- The destination host must acknowledge that it received each TCP segment.
- If a segment isn't acknowledged, it is sent again.

TCP provides sequencing.
- Sequence numbers in the TCP header allow destination hosts to put segments in the correct order even if they arrive out of order.

TCP provides flow control
- The destination host can tell the source host to increase/decrease the rate that data is sent.

Three-way Handshake
SYN flag sent by source
SYN flag, ACK flag sent by dest
ACK flag sent by source

Four-way Handshake
FIN flag sent by source
ACK flag sent by dest
FIN flag sent by dest
ACK flag sent by source

Hosts set a random initial sequence number.
Forward acknowledgement is used to indicate the sequence number of the next segment the hosts expects to receive.

Acknowledging every single segment, no matter what size, is inefficient.
The TCP header's Window Size field allows more data to be sent before an acknowledgment is required.
A 'sliding window' can be used to dynamically adjust how large the window size is.

UDP (User Datagram Procotol)
UDP is not connection-oriented
- The sending host does not establish a connection with the destination host before sending data. The data is simply sent.

UDP does not provide reliable communication
- When UDP is used, acknowledgments are not sent for received segments. If a segment is lost, UDP has no mechanism to re-transmit it. Segments are sent 'best-effor'.

UDP does not provide sequencing
- There is no sequence number field in the UDP header. If segments arrive out of order, UDP has no mechanism to put them back in order.

UDP does not provide flow control.
- UDP has no mechanism like TCP's window size to control the flow of data.

![image](https://github.com/fernskers/CCNA/assets/57144399/a072f733-8b1f-458f-8107-72137f411397)

TCP provides more features than UDP, but at the cost of additional overhead.
For applications that require reliable communications (for expample downloading a file), TCP is preferred.
For applications like real-time voice and video, UDP is preferred.
There are some applications that use UDP, but provide reliability etc. within the application itself.
Some applications use both TCP & UDP, depending on the situation.

![image](https://github.com/fernskers/CCNA/assets/57144399/807a3fe6-4d45-40d3-8782-883849277205)

Port Number to memorize
*TCP*
- FTP data (20)
- FTP control (21)
- SSH (22)
- Telnet (23)
- SMTP (25)
- HTTP (80)
- POP3 (110)
- HTTPS (443)

*UDP*
- DHCP server (67)
- DHCP client (68)
- TFTP (69)
- SNMP agent (161)
- SNMP manager (162)
- Syslog (514)

*TCP & UDP*
- DNS (53)

'Internet Stream Protocol' was developed in the late 1970s, but never actually introduced for public use.
It was never called 'IPv5', but it used a value of 5 in the Version field of the IP header.
So, when the successor to IPv4 was being developed, it was named IPv6.

The main reason IPv6 exists is that there is simply aren't enough IPv4 address available
There are 4,294,967,296 (2^32) IPv4 addresses available.
VLSM, private IPv4 addresses, and NAT have been used to conserve the use of IPv4 address space.
Those are short-term solutions, IPv6 is the long term solution.

IPv4 address assignments are controlled by IANA (Internet Assigned Numbers Authority)
IANA distributes IPv4 address space to various RIRs (Regional Internet Registries), which then assign them to companies that need them.

An IPv6 address is 128 bits.
4*the bits of an IPv4 address = 4*the number of possible addresses? NO
Every additional bit doubles the number of possible addresses.
There are 340,282,366,920,938,463,463,374,607,431,768,211,456 IPv6 addresses.

Shortening IPv6 addresses:
Leading 0s can be removed.
Consecutive quartets of all 0s can be replaced with a double colon (::)
Consecutive quartets of 0s can only be abbreviated once in an IPv6 address.

Finding the IPv6 prfix (global unicast addresses)
- Typically, an enterprise requesting IPv6 addresses from their ISP will receive a /48 black.
- Typically, IPv6 subnets use a /64 prefix length.
- That means an enterprise has 16 bits to use to make subnets
- The remaining 64 bits can be used for hosts.

Configuring IPv6 addresses
*ipv6 unicast-routing* allows the router to perform IPv6 routing
*ipv6 address <ipv6-address/mask>*
*show ipv6 interface brief*

EUI stands for Extended Unique Identifier
EUI-64 is a method of converting a MAC address (48 bits) into a 64-bit interface identifier.
This interface identifier can then become the 'host portion' of a /64 IPv6 address.
How to convert the MAC address:
1. Divide the MAC address in half
2. Insert FFFE in the middle
3. Invert the 7th bit

*ipv6 address <ipv6-address/mask> eui-64*

Global unicast IPv6 addresses are public addresses which can be used over the Internet.
Must register to use them. Because they are public addresses, it is expected that they are globally unique.
Originally defined as the 2000::/3 block


Unique local IPv6 addresses are private addresses which cannot be used over the Internet
You do not need to register to use them. They can be used freely within internal networks and don't need to be globally unique (*). Can't be routed over the Internet.
Uses the address block FC00::/7 
However, a later update requires the 8th bit to be set to 1, so the first two digits must be FD.
The global ID should be unique so that addresses don't overlap when companies merge.

Link-local IPv6 addresses are automatically generated on IPv6 enable interfaces.
Use command *ipv6 enable* on an interface to enable IPv6 on an interface
Uses the address block FE80::/10
However, the standard states that the 54 bits after FE80/10 should be all 0, so you won't see link local addresses beginning with FE9, FEA, or FEB. Only FE8.
The interface ID is generated using EUI-64 rules.
Link-local means that these addresses are used for communication within a single link (subnet).
  Routers will not route packets with a link-local destination IPv6 address.
Common uses of link-local addresses:
  routing protocol peerings (OSPFv3 uses link-local addresses for neighbor adjacencies)
  next-hop addresses for static routes
  Neighbor Discovery Protocol (NDP, IPv6's replacement for ARP) uses link-local addresses to function.

Multicast addresses

IPv6 uses range FF00::/8 for multicast.
IPv6 doesn't use broadcast

![image](https://github.com/fernskers/CCNA/assets/57144399/a6eba978-2bfa-4a45-820b-806f116447c4)

IPv6 defines multiple multicast 'scopes' which indicate how far the packet should be forwarded.
The addresses in the previous slide all use the 'link-local' scope (FF02), which stays in the local subnet.
IPv6 multicast scopes:
  Interface-local (FF01): The packet doesn't leave the local device. Can be used to send traffic to a service within the local device.
  Link-local (FF02): The packet remains in the local subnet. Routers will not route the packet between subnets.
  Site-local (FF05): The packet can be forwarded by routers. Should be limited to a single physical location (not forwarded over a WAN).
  Organization-local (FF08): Wider in scope than site-local
  Global (FF0E): No boundaries. Possible to be routed over the Internet
![image](https://github.com/fernskers/CCNA/assets/57144399/d60c7a98-2c96-4cf4-a6fe-09e36ceaba79)

Anycast is a new feature of IPv6.
Anycast is 'one-to-one-of-many'
Multiple routers are configured with the same IPv6 address.
  They use a routing protocol to advertise the address.
  When hosts sends packets to that destination address, routers will forward it to the nearest router configured with that

There is no specific address range for anycast addresses. Use a regular unicast address (global unicast, unique local) and specify it as an anycast address *ipv6 address <ipv6-address/mask> anycast*

:: = Unspecified IPv6 address
Can be used when a device doesn't yet know its IPv6 address.
IPv6 default routes are configured to ::/0
IPv4 equivalent: 0.0.0.0

::1 = The loopback address
Used to test the protocol stack on the local device
Messages sent to this address are processed within the local device, but not sent to other devices.
IPv4 equivalent: 127.0.0.0/8 address range

An RFC (Request for Comments) is a publication from the ISOC (Internet Society) and associated organizations like the IETF (Internet Engineering Task Force), and are the official documents of Internet specifications, protocols, procedures, etc.
RFC 5952 is 'A Recommendation for IPv6 Address Text Representation'
Before this RFC, IPv6 address representation was more flexible e.g.
- You could remove leading 0s, or leave them
- You could replace all-0 quartets with ::, or leave them
- You could use upper-case 0xA,B,C,D,E,F, or lower-case 0xa,b,c,d,e,f
RFC 5952 suggests standardizing IPv6 address representation

Leading 0s MUST be removed
Double colon MUST be used to shorten the longest string of all-0s quartests
If there are two equal-length choices for the ::, use :: to shorten the one on the left.
Hexadecimal characters 'a','b','c','d','e', and 'f' MUST be written using lower-case, NOT upper-case

IPv6 Header:

![image](https://github.com/fernskers/CCNA/assets/57144399/13cc1302-83a9-44ee-98e5-2cf4537b24f9)

Version field - Fixed value of 6
Traffic Class field - Used for QoS (Quality of Service)
Flow Label field - Used to identify specific traffic 'flows'
Payload Lenght field - Indicates the length of the payload
The length of the IPv6 header isn't included because its fixed
Next Header field - Indicates the type of the 'next header' (e.g. TCP or UDP)
Hop Limit - The value in this field is decremented by 1 by each router that forwards it. 
Souce / Destination field - you should alr know.

An IPv6 solicited-node multicast address is calculated from a unicast address.
*ff02 :: 1 : ff* + Last 6 hex digits of unicast address

Neighbor Discovery Protocol (NDP) is a protocol used with IPv6.
It has various functions, and one of those functions is to replace ARP, which is no longer used in IPv6.
The ARP-like function of NDP uses ICMPv6 and solicited-node multicast addresses to learn the MAC address of other hosts.
Two messages types are used:
1) Neighbor Solicitation (NS) = ICMPv6 Type 135
2) Neighbor Advertisement (NA) = ICMPv6 Type 136

Another function of NDP allows hosts to automatically discover routers on the local network.
Two messages are used for this process:
1) Router Solicitation (RS) = ICMPv6 Type 133
   -Sent to multicast address FF02::2 (all routers).
   -Asks all routers on the local link to identify themselves.
   -Sent when an interface is enabled/host is connected to the network

2) Router Advertisement (RA) = ICMPv6 Type 134
   -Sent to multicast address FF02::1 (all nodes)
   -The router announces its presence, as well as other information about the link
   -These messages are sent in responce to RS messages.
   -They are also sent periodically, even if the router hasn't recieved an RS.

SLAAC (Stateless Address Auto-configuration)
Hosts use the RS/RA messages to learn the IPv6 prefix of the local link, and then automatically generate an IPv6 address.
Using the *ipv6 address <prefix/prefix-length> eui-64* command, you need to manually enter the prefix.
Using the *ipv6 address autoconfig* command, you don't need to enter the prefix. The device uses NDP to learn the prefix used on the local link.
The device will use EUI-64 to generate the interface ID, or it will be randomly generated.

Duplicate Address Detection (DAD) allows hosts to check if other devices on the local link are using the same IPv6 address.
Any time an IPv6-enabled interface initializes (no shutdown command), or an IPv6 address is configured on an interface (by any method: manual, SLAAC, etc.) it performs DAD.
DAD uses two messages you learned earlier: NS and NA.
The host will send an NS to its own IPv6 address. If it doesn't get a reply, it knows the address is unique.
If it gets a reply, it means another host on th enetwork is already using the address.

IPv6 Static Routing

IPv6 routing is disabled by default, and must be enabled with *ipv6 unicast-routing*
If IPv6 routing is disabled, the router will be able to send and receive IPv6 traffic, but will not route IPv6 (=will not forward it between networks).
*ipv6 route <destination/prefix-length {next-hop | exit-interface [next-hop]) [ad] 

Directly attached static route: Only the exit-interface is specified. (NOTE: In IPv6, you can't use directly attached static routes if the interface is an Ethernet interface)
  *ipv6 route destination/prefix-length exit-interface*

Recursive static route: Only the next hop is specified.
  *ipv6 route destination/prefix-length next-hop*

Fully specified static route: Both the exit interface and next hop are specified.
  *ipv6 route destination/prefix-length exit-interface next-hop*

ACLs (Access Control Lists) have multiple uses.
ACLs function as a packet filter, instructing the router to permit or discard specific traffic.
ACLs can filter traffic based on source/destination IP addresses, source/destination Layer 4 ports, etc.

ACLs are configured globally on the router.
They are an ordered sequence of ACEs. (Access Control Entries)

Configuring an ACL in global config mode will not make the ACL take effect.
The ACL must be applied to an interface.
ACLS are applied either inbound or outbound.
ACLs are mude up of one or more ACEs.
When the router checks a packet against the ACL, it processes the ACEs in order, from top to bottom.
If the packet matches one of the ACEs in the ACL, the router takes the action and stops processing the ACL. All entries below the matching entry will be ignored.

There is an 'implicit deny' at the end of all ACLs.
The implicit deny tells the router to deny all traffic that doesn't match any of the configured entries in the ACL.

Standard ACLs: Match based on Source IP address only
Extended ACLs: Match based on Source/Destination IP, Source/Destination port, etc.

Standard ACLs match traffic based only on the source IP address of the packet.
Numbered ACLs are identified with a number.
Different types of ACLs have a different range of numbers that can be used.
-> Standard ACLs can use 1-99 and 1300-1999.

The basic command to configure a standard numbered ACL is:
*access-list <number> {deny | permit} <ip> <wildcard-mask>*

*do show access-lists* 
*access-list <num> remark <comment>*

applying to an interface: *ip access-group <num> {in | out}*
Standard ACLs should be applied as close to the destination as possible.

Named ACLs are identified with a name (ie. BLOCK_BOB)
Standard named ACLs are configured by entering 'standard named ACL config mode', and then configuring each entry within that config mode.
*ip access-list standard <acl-name>*
*<entry-number> {deny | permit} <ip> <wildcard-mask>*

Advantages of named ACL config mode:
- You can easily delete individual entries in the ACL with *no <sequence-number>*
- You can insert new entries in between other entries by specifying the sequence number

Reqseuencing ACLs
- There is a resequencing function that helps edit ACLs
- The command is *ip access-list resequence <acl-id> <starting-seq-num> <increment>*

Extended ACLs
- Extended ACLs function mostly the same as standard ACLs.
- They can be numbered or named, just like standard ACLs.
  - Numbered ACLs use the following ranges: 100 - 199, 2000 - 2699
- They are processed from top to bottom, just like standardd ACLs
- However, the can match traffic based on more parameters, so they are more precise than standard ACLs
- *access-list <number> (permit | deny) <protocol> <src-ip> <dest-ip>*
- *ip access-list extended (name | number)*
- *(seq-num) (permit | deny) <protocol> <src-ip> <dest-ip>*

In extended ACLs, to specify a /32 source or destination you have to use the host option or specify the wildcard mask. You can't just write the address without either of those.

When matching TCP/UDP, you can optionally specify the source and/or destination port numbers to match.

eq 80 = equal to port 80
gt 80 = greater than 80 (81 and greater)
lt 80 = less than 80 (79 and less)
neq 80 = NOT 80
range 80 100 = from port 80 to port 100

Extended ACLs should be applied as close to the source as possible, to limit how far the packets travel in the network being denied.

Layer 2 discovery protocols such as CDP and LLDP share information with and discover information about neighboring (connected) devices.
The shared information includes host name, IP address, device type, etc.
CDP is a Cisco proprietary protocol.
LLDP is an industry standard protocol (IEEE 802.1AB)
Because they share information about the devices in the network, they can be considered a security risk and are often not used. It is up to network engineer/admin to decide if they want
to use thme in the network or not.

CDP (Cisco Discovery Protocol)
- It is enabled on Cisco devices by default.
- CDP messages are periodically sent to multicast MAC address 0100.0CCC.CCCC.
- When a device receives a CDP message, it processes and discards the message. It does NOT forward it to other devices.
- By default, CDP messages ar esent once every 60 seconds.
- By default, the CDP hold time is 180 seconds. If a message isn't received from a neighbor for 180 seconds, the neighbor is removed from the CDP neighbor table.
- CDPv2 messages are sent by default.

*show cdp*
*show cdp traffic*
*show cdp interface*
*show cdp neighbors*
*show cdp neighbors detail*
*show cdp entry <name>*

*cdp run* global only
*cdp enable* interface only
*cdp timer <seconds>*
*cdp holdtime <seconds>*
*cdp advertise-v2*

LLDP (Link Layer Discovery Protocol)
- It is usually disabled on Cisco by default, so it must be manually enabled.
- A device can run CDP and LLDP at the same time.
- LLDP messages are periodically sent to multicast MAC address 0180.C200.000E
- When a device receives an LLDP message, it processes and discards the message. It does NOT forward it to other devices.
- By default, LLDP messages are sent once every 30 seconds.
- By default, LLDP holdtime is 120 seconds.
- LLDP has an additional timer called the 'reinitialization delay'. If LLDP is enabled, this timer will delay the actual initialization of LLDP. 2 seconds by default.

*lldp run* globally enable
*lldp transmit* enable lldp transmissions on specific interfaces
*lldp receive* enable lldp receiving
*lldp timer <seconds>*
*lldp holdtime <seconds>*
*lldp reinit <seconds>*

*show lldp*
*show lldp traffic*
*show lldp interface*
*show lldp neighbors*

All devices have an internal clock (routers, switches, your PC, etc.)
In Cisco IOS, you can view the time with the *show clock* command.
The default time zone is UTC (Coordinated Universal Time).
If you use the *show clock detail* command, you can see the time source.
The internal hardware clock of a device will drift over time, so it is not the ideal time source.
From a CCNA perspective, the most important reason to have accurate time on a device is to have accurate logs for troubleshooting.

*show logging*

You can manually configure the time on the device with the *clock set* command.
*show clock detail*

Although the hardware calendar is the default time-source, the hardware clock and software clock are separate and can be configured separately.

You can manally configure the hardware clock with the *calendar set* command
*show calendar*

Typically you will want to synchronize the 'clock' and 'calendar'.
Use the command *clock update-calendar* to sync the calendar to the clock's time.
Use the command *clock read-calendar* to sync the clock to the calendar's time.

You can configure the time zone with the *clock timezone* command.

*clock summer-time <timezone>* daylight savings time

NTP (Network Time Protocol)
- Manually configuring the time on devices is not scalable
- The manually configured clocks will drift, resulting in inaccurate time.
- NTP (Netowrk Time Protocol) allows automatic syncing of time over a network.
- NTP clients request the time from NTP servers.
- A device can be an NTP server and an NTP client at the smae time.
- NTP allows accuracy of time within ~1 millisecond if the NTP server is in the same LAN, or within ~50 milliseconds if connecting to the NTP server over a WAN/the Internet
- Some NTP servers are 'better' than ohters. The 'distance' of an NTP server from the original reference clock is called stratum
- NTP uses UDP port 123 to communicate

Reference Clocks
- A reference clock is usually a very accurate time device like an atomic clock or a GPS clock.
- Reference clocks are stratum 0 within the NTP hierarchy.
- NTP servers directly connected to reference clocks are stratum 1.

Reference clocks are stratum 0.
Stratum 1 NTP servers get their time from reference clocks.
Stratum 2 NTP servers get their time form stratrum 1 NTP servers.
Stratum 3 NTP servers get their time form stratrum 2 NTP servers.
Stratum 15 is the maximum. Anything above that is considered unreliable.
Devices can also 'peer' with devices at the same stratum to provide more accurate time.
An NTP client can sync to multiple NTP servers.
NTP servers which get their time directly from reference clocks are also called primary servers.
NTP servers which get their time from other NTP servers are called secondary servers. They operate in server mode and client mode at the same time.

*ntp server <ip-add>*
*show ntp association*
*show ntp status*
*do show clock detail*
*ntp source <add>*
*ntp server <add>*

The default stratum of the ntp master command is 8.
NTP authentication can be configured, although it is optional.
It allows nTP clients to ensure they only sync to the intended servers.
*ntp authenticate* (enable NTP authentication)
*ntp authentication-key <key-number> md5 <key>* (create the NTP authentication key(s))
*ntp trusted-key <key-number>* (specify the trusted key(s))
*ntp server <ip-add> key <key-num>* (specify which key to use for the server)

DNS is used to resolve human-readable names (google.com) to IP addresses.
Machines such as PCs don't use names, they use addresses.
Names are much easier for us to use and remember than IP addresses.
-> What's the IP address of youtube.com
When you type 'youtube.com' into a web browser, your device will ask a DNS server for the IP address of youtube.com
The DNS server(s) your device uses can be manually configured or learned via DHCP.

DNS 'A' record = Used to map names to IPv4 addresses.
DNS 'AAAA' record = Used to map names to IPv6 addresses.
Standard DNS queries/responses typically use UDP. TCP is used for DNS messages greater than 512 bytes. In either case, port 53 is used.
Devices will save the DNS server's responses to a local DNS cache. This means they don't have to query the server every single time they want to access a particular destination.
*ipconfig /displaydns*
*ipconfig /flushdns* ->clear DNS resolve cache
Most devices have a hosts file /Windows/System32/drivers/etc/hosts (this is not DNS)

For hosts in a network to use DNS, you don't need to configure DNS on the routers. They will simply forward the DNS messages like any other packet.
However a Cisco router can be configured as a DNS server, although it's rare.
  -> If an internal DNS server is used, usually it's a Windows or Linux server.
A Cisco router can also be configured as a DNS client.
*ip dns server* (configure route to act as a dns server)
*ip host <name> <ip-add>* (configure a list of hostname/ip address mappings)
*ip name-server <ip-add>* (configure a DNS server that the router will query if the requested record isn't in its host table)
*ip domain lookup* (enable the router to perform DNS queries)
*show hosts*
*ip domain name <domain-name>* (configure the default domain name; this will be automatically appended to any hostnames without a specified domain)

DHCP is used to allow hosts to automatically/dynamically learn various aspects of their network configuration, such as IP address, subnet mask, default gateway, DNS server, etc, without manual/static configuration.
It is an essential part of modern networks.
Typically used for 'client devices' such as workstations, phones, etc.
Devices such as routers, servers, etc, are usulaly manually configured.
In small networks (such as home networks) the router typically acts as the DHCP server for hosts in the LAN.
In larger networks, the DHCP server is usually a Windows/Linux server.

DHCP server 'lease' IP address to clients.
These leases are usually not permanent, and the client must give up the address at the end of the lease.

*ipconfig /release* release dhcp learned ip address

DHCP servers use UDP 67.
DHCP clients use UDP 68.

*ipconfig /renew* get an ip address from a dhcp server

DHCP Discover, a broadcast message from the client. 
DHCP Offer, server to client, offering an IP address.
DHCP Request, client to server, wants to use the IP address that was offered.
DHCP Ack, server to client, "you can use it lil bro".

![image](https://github.com/fernskers/CCNA/assets/57144399/9fa27202-5bf1-4707-a65a-4f1324b1c811)

Some network engineers might choose to configure each router to acta s the DHCP server for its connected LANs.
However, large eterprises often choose to use a centralized DHCP server.
If the server is centralized, it won't receive the DHCP clients' broadcast DHCP messages. (broadcast messages don't leave the local subnet)
To fix this, you can configure a router to act as a DHCP relay agent.
The router will forward the clients' broadcast DHCP messages to the remote DHCP server as unicast messages.

*ip dhcp excluded-address <ip-add> <ip-add>* (specify a range of addresses that won't be given to DHCP clients.
*ip dhcp pool <name>* (create a DHCP pool)
*network <ip-add> <mask>* (specify the subnet of addresses to be assigned to clients)
*dns-server <ip-add>* (specify the DNS server that DHCP clients should use)
*domain-name <domain-name>* (specify the domain name of the network)
*default-router <ip-add>* (specify the default gateway)
*lease <days> <hours> <minutes>* (specify the lease time)
*show ip dhcp binding* 
*ip helper-address <ip-add>* (configure the ip address of the dhcp server as the 'helper' address)(This command is used at the interface connected to the subnet of the client devices)
*ip address dhcp* (tell the router to use DHCP to learn its IP address)

Simple Network Management Protocol
- SNMP is an industry-standard framework and protocol that was originally released in 1988.
- RFC 1065 Structure and identification of management information for TCP/IP-based internets
- RFC 1066 Managemnt information base for netowrk management of TCP/IP-based interfnets
- RFC 1067 A simple network management protocol
- SNMP can be used to monitor the status of devices, make configuration changes, etc.
- There are two main types of devices in SNMP:
- 1) Managed Devices (There are the devices being managed using SNMP, e.g. network devices like routers and switches)
  2) Network Management Station (NMS) (The device/devices managing the managed devices, this is the SNMP 'server')

There are three main operations used in SNMP.
1) Managed devices can notify the NMS of events.
2) The NMS can ask the managed devices for information about their current status.
3) The NMS can tell the managed devices to change aspects of their configuration.

The SNMP Manager is the software on the NMS that interacts with the managed devices.
  It recieves notications, sends requests for information, sends configuration changes, etc.
The SNMP Application provides an interface for the network admin to interact with.
 Displays alerts, statistics, charts, etc.

The SNMP Agent is the SNMP software running on the managed devices that interacts with the SNMP Manager on the NMS.
 It sends notifications to/receives messages form the NMS.

The Management Information Base (MIB) is the structure that contains the variables that are managed by SNMP
  Each variable is identified with an Object ID (OID)
  Example variables: Interface status, traffic throughput, CPU usage, temperature, etc.

SNMP Object IDs are organized in a hierarchical structure.

Many versions of SNMP have been proposed/developed, however only three major versions have achieved wide-spread use:

SNMPv1
  The original version of SNMP
SNMPv2c
  Allows the NMS to retrieve large amounts of information in a single request, so it is more efficient.
  'c' refers to the 'community strings' used as passwords in SNMPv1, removed from SNMPv2, and then added back for SNMPv2c.
SNMPv3
  A much more secure version of SNMP that suppoerts strong encryption and authentication. Whever possible, this version should be used!
  
![image](https://github.com/fernskers/CCNA/assets/57144399/6e546c57-9e6e-47a3-a9d7-0c5acfb3d0b2)

SNMP Agent = UDP 161
SNMP Manager = UDP 162

*snmp-server contact <contact-name>* optional
*snmp-server location <location-name>* optional
*snmp-server community <communitystringname> <ro | rw>* ro = read only rw = read/write (can use Set messages)
*snmp-server host <ip-add> version <version> <communitystringname>*
*snmp-server enable traps snamp linkdown linkup* (configure the trap types to send to the nms)
*snmp-server enable traps config*

In SNMPv1 and SNMPv2c, there is no encryption. The community and message contents are sent in plain-text. This is not
secure, as the packets can easily be captured and read.

![image](https://github.com/user-attachments/assets/d94a9990-7f20-4fa4-93c3-eb1a4c09e512)

By default, no password is needed to access the CLI of a CIsco IOS device via the console port.
You can configure a password on the console line.
  A user will have to enter a password to access the cLI via the console port.

*line console 0* (There is only a single console line, so the number is always 0)
*password <passowrd>* (Configure the console line's password)
*login* (Tell the device to require a user to enter the configured password to access the CLI via the console port.)

Alternatively, you can configure the console line to require users to login using one of the configured usernames on the device.

*username <user> secret <passwrd>*
*line console 0*
*login local* (Tell the device to require a user to login using one of the configured usernames on the device)

Layer 2 switches don't perform packet routing and don't build a routing table. They aren't IP routing aware.
However, you can assign an IP address to an SVI to allow remote connections to the CLI of the switch (using Telnet or SSH).
Configure the IP address on the SVI in the same way as on a multilayer switch. Enable the interface if necessary.
Configure the switch's default gateway. In this case, PC2 isn't in the same LAN as SW1. If SW1 doesn't have a default gateway, it can't communicate with PC2.

Telnet (Teletype Newtork) is a protocol used to remotely access the CLI of a remote host.
Telnet was developed in 1969.
Telnet has been largely replaced by SSH, which is more secure.
Telnet sends data in plain text.
Telnet server listens for Telnet traffic on TCP port 23.

*enable secret <passwrd>* (If an enable password isn't configured,  you won't be able to access privileged exec mode when connecting via Telnet)
*access-list <num> permit host <ip-add>* (configure an ACL to limit which devices can connect to the VTY lines)
*line vty 0 15* (Telnet/SSH access is configured on the VTY lines. There are 16 lines available, so up to 16 users can be connected at once. (VTY stands for Virtual TeleType)
*login local*
*exec-timeout 5 0*
*transport intput telnet* (allows only Telnet connections)
*access-class 1 in* (apply the ACL to the VTY lines, access-class applies an ACL to the VTY lines, ip access-group applies an ACL to an interface)

SSH (Secure Shell) was developed in 1995 to replace less secure protocols like Telnet.
SSHv2, a major revision of SSHv1, was realeased in 2006.
If a device supports both version 1 and version 2, it is said to run 'version 1.99'.
Provides security features such as data encryption and authentication.
The SSH server listens for SSH traffic on TCP port 22.

IOS images that support SSH will ahve 'K9' in their name.
Cisco exports NPE (No Payload Encryption) IOS images to countries that have restrictions on encryption technologies.
NPE IOS images do not support cryptographic features such as SSH.

*show version*
*show ip ssh*

To enable and use SSH, you must generate an RSA public and private key pair.
The keys are used for data encryption/decryption, authentication, etc.

*ip domain name <domain-name>* (The FQDN of the device is used to name the RSA keys. FQDN = Fully Qualified Domain Name (host name + domain name).
*crypto key generate rsa* (Generate the RSA keys. * length must be 768 bits or greater for SSHv2)
*do show ip ssh*

*enable secret <passwd>*
*username <user> secret <passwd>*
*access-list <num> permit host <ip-add>*
*ip ssh version <num>* (optional, but recommended) (Restrict SSH to version 2 onlly)
*line vty 0 15* (configure all vty lines, just like telnet)
*login local* (enable local user authentication *you cannot use *login* for SSH, only *local login*)
*exec-timeout <min> <sec>* (optional, but recommended) configure the exec timeout
*transport input ssh* (best practice is to limit vty line connections to SSH only
*access-class <num> in* (optional, but recommended) apply the acl to restrict Vty line connections

SSH Configuration
1) Configure host name
2) Confiure DNS domain name
3) Generate RSA key pair
4) Configure enable PW, username/PW
5) Enable SSHv2 (only)
6) Configure VTY lines

FTP (File Transfer Protocol) and TFTP (Trivial File Transfer Protocol) are industry standard protocols used to transfer files over a network.
They both use a client-server model.
-> Clients can use FTP or TFTP to copy files from a server.
-> Clients can use FTP or TFTP to copy files to a server.
As a network engineer, the most common use for FTP/TFTP is in the process of upgrading the operating system of a network device.
You can use FTP/TFTP to download the newer version of IOS from a server, and then reboot the device with the new IOS image.

TFTP was first standardized in 1981.
Named 'Trivial' because it is simple and has only basic features compared to FTP.
-> Only allows a client to copy a file to or from a server.
Was released after FTP, but is not a replacement for FTP. It is another tool to use when lightweight simplicity is more impmortant than functionality.
No authentication (username/PW), so servers will respond to all TFTP requests.
No encryption, so all data is sent in plain text.
Best used in a controlled environment to transfer small files quickly
TFTP servers listen on UDP port 69.
UDP is connectionless and doesn't provide reliabiltiy with rtransmissions.
However, TFTP has similar built-in features within the protocol itself.

Every TFTP data message is acknowledged.
-> If the client is transferring a file to the servver, the server will send Ack messages.
-> If the server is transferring a file to the client, the client will send Ack messages.
Timers are used, and if an expected message isn't received in time, the waiting device will re-send its previous message.
TFTP uses 'lock-step' communication. The client and server alternately send a message and then wait for a reply. (+ retransmissions are sent as needed)

TFTP 'Connections'
TFTP file transfers have three phases:
1: Connection: TFTP client sends a request to the server, and the server responds back, initializing the connection.
2: Data Transfer: The client and server exchange TFTP messages. One sends data and the other sends acknowledgments.
3: Connection Termination: After the last data message has been sent, a final acknowledgment is sent to terminate the connection.

FTP was first standardized in 1971.
FTP uses TCP ports 20 and 21.
Usernames and passwords are used authentication, however there is no encryption.
For greater security, FTPS (FTP over SSL/TLS) can be used.
SSH File Transfer Protocol (SFTP) can also be used for greater security.
FTP is more complex than TFTP and allows not only file transfers, but clients can also navigate file directories, add and remove directories, list files, etc.
The client sends FTP commands to the server to perform these functions.

FTP uses two types of connections:
-> An FTP control connection (TCP 21) is established and used to send FTP commands and replies.
When files or data are to be transferred, separate FTP data (TCP 20) connections are established and termiated as needed.

The default method of establishing FTP data connections is active mode, in which the server initiates the TCP connection.
In FTP active mode, the server initiates the data connection.

In FTP passive mode, the client initiates the data connection. This is often necessary when the client is behind a firewall, which could block the incoming connection from the server.
Firewalls usually don't permit 'outside' devices to initiate connections. In this case, FTp passive mode is used and the client (behind firewall) initiates the TCP connection.

![image](https://github.com/user-attachments/assets/4da84dfd-020d-495f-9208-1d4ec4e88bf9)

IOS File Systems

A file system is a way of controlling how data is stored and retrieved.
You can view the file systems of a Cisco IOS device with *show file systems*
disk - storage devices such as flash memory
opaque - used for internal functions
nvram - internal nvram. the startup-config file is stored here
network - represents external file systems e.g. external FTP/TFTP servers.

You can view the current version of IOS with *show version*
You can view the contents of flash with *show flash*

Copying files (TFTP)
*copy tftp: <destination>*

*boot system <filepath>* (boots this ios file)
*delete <filepath>* deletes file

Copying Files (FTP)
*ip ftp username <user>*
*ip ftp password <pass>*
*copy ftp: <dest>*

IPv4 doesn't provide enough addresses for all devices that need an IP address in the modern world.
The long-term solution is to switch IPv6.
There are three main short-term solutions:
1) CIDR
2) Private IPv4 addresses
3) NAT

RFC 1918 specifies the following IPv4 address ranges as private:
10.0.0.0/8 (10.0.0.0 to 10.255.255.255)
172.16.0.0/12 (172.16.0.0 to 172.31.255.255)
192.168.0.0/16 (192.168.0.0 to 192.168.255.255)

Private IP addresses cannot be used over the Internet!
Two problems:
1) Duplicate addresses
2) Private IP addresses can't be used over the Internet, so the PCs can't access the Internet.

Network Address Translation (NAT) is used to modify the source and/or destination IP addresses of packets.
There are various resasons to use NAT, but the most common reason is to allow hosts with private IP addresses to communicate with other hosts over the Internet.

Static NAT involves statically configuring one-to-one mappings of private IP addresses to public IP addresses.
An inside local IP address is mapped to an inside global IP address.
  -> Inside Local = The IP address of the inside host, from the perspective of the local network *ip address actually ocnfiugred on the inside host, usually priv address
  -> Inside Global = The IP address of the inside host, from the perspective of outside hosts. *ip address of the inside host after NAT, usually public address
  -> Outside Local = The IP address of the outside host, from the perspective of the local network
  -> Outside Global = The IP address of the outside host, from the perspective of the outside network
  
Static NAT allows devices with private IP addresses to communicate over the Internet. However, because it requires a one-to-one IP address mapping, it doesn't help preserve IP addresses.
*ip nat inside* (define the 'inside' interface connected to the internal network)
*ip nat outside* (define the 'outside' interface connected to the external network)
*ip nat inside source static <inside-local-ip> <inside-global-ip>* (configure the one-to-one IP address mappings)
*show ip nat translations*
*clear ip nat translation <astrik>*
*show ip nat statistics*

In dynamic NAT, the router dynamically maps inside local addresses to inside global adddresses as needed.
An ACL is used to identify which traffic should be translated.
  If the source IP is permitted by the ACL, the source IP will be translated.
  If the source IP is denied by the ACL, the source IP will NOT be translated. (traffic won't be dropped)
A NAT pool is used to define the available inside global addresses that can be used.
Although they are dynamically assigned, the mappings are still one-to-one (one inside local IP address per inside global IP address)
If there aren't enough inside global IP addresses available, it is called 'NAT pool exhaustion'.
  If a packet from another inside host arrives and needs NAT but there are no available addresses, the router will drop the packet.
  The host will be unable to access outside networks until one of the inside global IP addresses becomes available.
  Dynamic NAT entries will time out automatically if not used, or you can clear them manually.

*ip nat inside* (define the inside interfaces connected to the internal network)
*ip nat outside* (define the outside interface connected to the external network)
*access-list <num> permit <ip-add> <sub-mask>* (define the traffic that should be translated)
*ip nat pool <pool-name> <glob-ip-add> <glob-ip-add> prefix-length <mask-num>* (define the pool of inside global IP addresses)
*ip nat inside source list <acl-num> pool <pool-name>* (configure dynamic NAT by mapping the acl to the pool)

PAT (aka NAT overload) translates both the IP address and the port number.
By using a unique port number for each communication flow, a single public IP address can be used by many different internal hosts. (port numbers are 16 bits = over 65,000 available port numbers)
The router will keep track of which inside local address is using which inside global address and port.
Because many inside hosts can share a single public IP, PAT is very useful for preserving public IP addresses, and it is used in networks all over the world.

*ip nat inside source list <acl-num> pool <pool-name> overload* (configure PAT)
*ip nat inside source list <acl-num> interface <out-interface> overload* (configure PAT by mapping the ACL to the interface and enabling overload*

Traditional phones operate over the public switched telephone network (PSTN)
Sometimes this is called POTS (Plain Old Telephone Service)
IP phones use VoIP (Voice over IP) technologies to enable phone calls over an IP network, such as the Internet.
IP phones are connected to a switch just like any other end host.

IP phones have an internal 3-port switch.
 1 port is the 'uplink' to the external switch.
 1 port is the 'downlink' to the PC.
 1 port connects internally to the phone itself.
This allows the PC and the IP phone to share a single switch port. Traffic from the PC passes through the IP phone to the switch.
It is recommended to seperate 'voice' traffic (from the IP phone) and 'data' traffic (from the PC) by placing them in separate VLANs.
  This can be accomplished using a voice VLAN.
  Traffic from the PC will be untagged, but traffic from the phone will be tagged with a VLAN ID
  
*switchport voice vlan <num>* (Swith will use CDP to tell phone to tag its traffic in VLAN)

Although the interface sends/receives traffic from two VLANs, it is not considered a trunk port. It is considered an access port.

PoE allows Power Sourcing Equipment (PSE) to provide power to Powered Devices (PD) over an Ethernet cable.
Typically the PSE is a switch and the PDs are IP phones, IP cameras, wireless access points, etc.
The PSE receives AC power from the outlet, converts it to DC power, and supplies that DC power to the PDs.
Too much electrical current can damage electrical devices.
PoE has a process to determine if a connected device needs power, and how much power it needs.
  When a device is connected to a PoE-enabled port, the PSE (switch) sends low power signals, monitors the response, and determines how much power the PD needs.
  If the device needs power, the PSE supplies the power to allow the PD to boot.
  The PSE continues to monitor the PD and supply the required amount of power
Power policing can be configurind to prevent a PD from taking too much power.
  *power incline police* configures power policing with the default settings: disalbe the port and send a Syslog message if a PD draws too much power.
    equivalent to *power inline police action err-disable*
    the interface will be put in an 'error-disabled' state and can be re-enabled with shutdown followed by no shtudown.
  *power inline police action log* does not shut down the interface if the PD draws too much power. It will restart the interface and send a Syslog message.

![image](https://github.com/user-attachments/assets/63f0c877-3aa4-45bf-95c1-8bc9df643999)

Voice traffic and data traffic used to use entirely separate networks.
  Voice traffic used the PSTN
  Data traffic used the IP network (enterprise WAN, Internet, etc.)
QoS wasn't necessary as the different kinds of traffic didn't compete for bandwidth.

Modern networks are typically converged networks in which IP phones, video traffic, regular data traffic, etc. all share the same IP network
This enable cost saings as well as more advanced features for voice and video traffic, for example integrations with collaboraiton software (Cisco WebEx, Microsoft Teams, etc).
However, the different kinds of traffic now have to compete for bandwidth.
QoS is a set of tools used by network devices to apply different treatment to different packets.

QoS is used to manage the following characteristics of network traffic:
1) Bandwidth
   The overall capactiy of the link, measured in bits per second (Kbps, Mbps, Gbps, etc)
   QoS tools allow you to reserve a certain amount of a link's bandwidth for specific kinds of traffic.
     For example: 20% voice traffic, 30% for specific kinds of data traffic, leaving 50% for all other traffic.

2) Delay
   The amount of time it takes traffic to go from source to destination = one-way delay
   The amount of time it takes traffic to go from source to destination and return = two-way delay

3) Jitter
   The variation in one-way delay between packets sent by the same application
   IP phones have a 'jitter buffer' to provide a fixed delay to audio packets.
   
4) Loss
   The % of packets sent that do not reach their destination
   Can be caused by faulty cables
   Can also be caused when a device's packet queues get full and the device starts discarding packets.
   
The following standards are recommended for acceptable interactive audio (ie. phone call) quality:
One-way delay: 150 ms or less
Jitter: 30 ms or less
Loss: 1% or less
If these standards are not met, there could be a noticeable reduction in the qulity of the phone call.

If a network device receives messages fsater than it can forward them out of the appropriate interface, the messages are placed in a queue.
By default, queued messages will be forwarded in a First In First Out (FIFO) manner.
  Messages will be sent in the order thay are received.
If the queue is full new packets will be dropped.
This is called tail drop.

Tail drop is harmful because it can lead to TCP global synchrionization.
Review of the TCP sliding window:
  Hosts using TCP use the 'sliding window' increase/decrease the rate at which they send traffic as needed.
  When a packet is dropped it will be retransmitted.
  When a drop occurs, the sender will reduce the rate it sends traffic.
  It will then gradually increase the rate again.
When the queue fills up and tail drop occurs, all TCP hosts sending traffic will slow down the rate at which they send traffic.
They will all then increase the rate at which they send traffic, which rapidly leads to more congestion, dropped packts, and the process repeats again.

A solution to prevent tail drop and TCP global synchronization is Random Early Detection (RED).
When the amount of traffic in the queue reaches a certain threshold, the device will start randomly dropping packets from select TCP flows.
Those TCP flows that dropped packets will reduce the rate at which traffic is sent, but you will avoid gloval TCP syncronization, in which ALL TCP flows reduce and then 
increase the rate of transmission at the same time in waves.

In standard RED, all kinds of traffic are treated the same.
An improved version, Weighted Random Early Detection (WRED), allows you to control which packets are dropped depending on the traffic class.

The purpose of QoS is to give certain kinds of network traffic priority over others during congestion.
Classification organizes network traffic (packets) into traffic classes (categories).
Classification is fundamental to QoS. To give priority to certain types of traffic, you have to identify whicih types of traffic to give priority to.
There are many methods of classifying traffic. e.g.
  An ACL. Traffic which is permitted by the ACL will be given certain treatement, other traffic will not.
  NBAR (Network Based Application Recongnition) performs a deep packet inspection, looking beyond the Layer 3 and Layer 4 information up to Layer 7 to identify the specific kind of traffic.
  In the Layer 2 and Layer 3 headers there are specific fields used for this purpose.

The PCP (Priority Code Point) field of the 802.1Q tag (in the Ethernet header) can be used to identify high/low priority traffic.
  Only when there is a dot1q tag
The DSCP (Differentiated Services Code Point) field of the IP header can also be used to identify high/low priority traffic.
PCP is also known as CoS (Class of Service). Its use is defined by IEEE 802.1p
3 bits = 8 possible values (2^3 = 8)

![image](https://github.com/user-attachments/assets/50a28339-a844-4418-9bb9-7a05a0ff09c9)

Best effort delivery means there is no guarentee that data is delivered or that it meets any QoS stadard. This is regular traffic, not high-priority.
IP phones mask call signaling traffic (used to establish calls) as PCP3. They mark the actual voice traffic as PCP5.
Because PCP is found in the dot1q header, it can only be used over the following connections:
  trunk links
  access links with a voice VLAN

RFC 2474 (1998) defines the DSCP field, and other 'DiffServ' RFCs elaborate on its use.
With IPP updated to DSCP, new standard marking had to be decided upon.
  By having generally agreed upon standard markings for different kinds of traffic, QoS design & implementation is simplified, QoS works between ISPs and enterprises, among other benefits.

Default Forwarding (DF) - best effor traffic
Expedited Forwarding (EF) - low loss/latency/jitter traffic (usually voice)
Assured Forwarding (AF) - A set of 12 standard values
Class Selector (CS) - A set of 8 standard values, provides backward compatibility with IPP

Default Forwarding - best effor
The DSCP marking for DF is 0.
EF is used for traffic that requires low loss/latency/jitter.
The DSCP marking for EF is 46.

AF defines four traffic classes. All packets ina  class have the same priority.
Within each class, there are three levels of drop precedence.
  Higher drop precedence = more likely to drop the packet during congestion.

![image](https://github.com/user-attachments/assets/69fa24c9-14e4-4ed2-a1f0-22c9621ca471)

CS defines eight DSCP values for backward compatibility with IPP.
The three bits that were added for DSCP are set to 0, and the originial IPP bits are used to make 8 values.

RFC 4954 was developed with the help of Cisco to bring all of these values together and standardize their use.
The RFC offers many specific recommendations, but here are a few key ones:
Voice traffic: EF
Interactive video: AF4x
Streaming video: AF3x
High priority data: AF2x
Best effort: DF

The trust boundary of a network defines where devices trust/don't trust the QoS markings of received messages.
If the markings are trusted, the device will forward the message without changing the markings.
IF the markings aren't trusted, the device will change the markings according to the configured policy.
If an IP phone is connected to the switch port, it is recommeded to move the trust boundary to the IP phones.
This is done via configuraiton on the switch port connected to the IP phone.
If a user marks their PC's traffic with high priority, the marking will be changed (not trusted).

An essential part of QoS is the use of multiple queues.
  This is where classification plays a role. The device can match traffic based on various factors (for exmpale the DSCP marking in the IP header) and then place it in the appropriate queue.
However, the device is only able to forward on efrom out of an interface at once, so a scheduler is used to decide which queue traffic is forwarded from next.
  Prioritization allows the schedular to give certain queues more priority than others.

A common scheduling method is weighted round-robin.
  round-robin = packets are taken from each queue in order, cyclically
  weighted = more data is taken from high priority queues each time the scheduler reaches that queue

CBWFQ (Class-Based Weighted Fair Queuing) is a popular method of scheduling, using a weighted round-robin scheduler while guaranteeing each queue a certain percentage of the interface's 
bandswidth during congestion.

Round-robin scheduling is not ideal for voice/video traffic. Even if the voice/video traffic receives a guarenteed minimum amount of bandwidth, round-robin can add delay and jitter because
even the high priority queues have to wait their turn in the scheduler. 

LLQ (Low Larency Queuing) designates one (or more) queues as strict priority queues.
  This means that if there is traffic in the queue, the scheduler will always take the next packet from that queue until it is empty.
This is very effective for reducing the delay and jitter of voice/video traffic.
However, it has the down side of potentially starving other queues if there is always traffic in the designated strict priority queue.
  Policing can control the amount of traffic allowed in the strict priority queue so that it can't take all of the link's bandwidth.

Traffic shaping and policing are both used to control the rate of traffic.
Shaping buffers traffic in a queue if the traffic rate goes over the configured rate.
Policing drops traffic if the traffic rate goes over the configured rate.
  'Burst' traffic over the configured rate is allowed for a short period of time.
  This accommodates data applications which typically are 'bursty' in nature. Instead of a constant stream of data, they send data in bursts.
  The amount of burst traffic allowed is configurable.
In both cases, classificiation can be used to allow for different rates for different kinds of traffic.

The principles of the CIA Triad form the foundation of security:
Confidentiality
  Only authorized users should be able to access data.
  Some information/data is public and can be access by anyone, some is secret and should only be accessed by specific people.

Integrity
  Data should not be tampered with by unauthorized users.
  Data should be correct and authentic.

Availability
  The network/systems should be operational and accessible to authorized users.

A vulnerability is any potential weakness that can compromise the CIA of a system/info.
  A potential weakness isn't a problem on its own.

An exploit is something that can potentially be used to exploit the vulnerability.
  Something that can potentially be used an exploit isn't a problem on it's own.

A threat is the potential of a vulnerability to be exploited.
  A hacker exploiting a vulnerability in your system is a threat.

A mitigation technique is something that cna protect against threats.
  Should be implemented everywhere a vulnerability can be exploited: client devices, servers, switches, routers, firewalls, etc.

DoS attacks threaten the availability of a system.
One common DoS attack is the TCP SYN flood.
  TCP three-way handshake: SYN | SYN-ACK | ACK
  The attacker sends countless TCP SYN messages to the target.
  The target sends a SYN-ACK message in response to each SYN it receives.
  The attacker never replies with the final ACK of the TCP three-way handshake.
  The incomplete connections fill up the target's TCP connection table.
  The attacker continues sending SYN messages.
  The target is no longer able to make legitimate TCP connections.

DDoS attack, the attacker infects many target computers with malware and uses them all to inititate a denial-of-service attack, for example a TCP SYN flood attack.
This group of infected computers is called a botnet.

To spoof an address is to use a fake source address (IP or MAC address).
Numerous attacks involve spoofing, it's not a single kind of attack.
An example is a DHCP exhaustion attack.
An attacker uses spoofed MAC addresses to flood DHCP Discover messages.
The target server's DHCP pool becomes full, resulting in a denial-of-service to other devices.

In a reflection attack, the attacker sends traffic to a reflector, and spoofs the source address of it spackets using the target's IP address.
The reflector (ie. A DNS Server) sends the reply to the target's IP address.
If the amount of traffic sent to the target is large enough, this can result in a denial-of-service.
A reflection attack becomes an amplification attack when the amount of traffic sent by the attacker is small, but it triggers a large amount of traffic to be sent from the reflector to the target.

In a man-in-the-middle attack, the attacker places himself between the source and destination to eavesdrop on communications, or to modify traffic before it reaches the destination.
A common example is ARP spoofing, also known as ARP poisoning.
A host sends an ARP request, asking for the MAC address of another device.
The target of the request sends an ARP reply, information the requester of its MAC address.
The attacker waits ands sends another ARP reply after the legitimate replier.
If the attacker's ARP reply arrives last, it will overwrite the legitimate ARP entry in PC1's ARP table.
In PC1's ARP table, the entry for 10.0.0.1 will have the attacker's MAC address.
When PC1 tries to send traffic to SRC1, it iwll be forwarded to the attacker instead.
The attacker can ispect the messages, and then forward them on to SRV1.
The attacker can also modify the message sbefore forwarding them to SRV1.
This compromises the Confidentiality and Integrity of communiactions between PC1 and SRV1.

Reconnaissance attacks aren't attacks themselves, but they are used to gather information about a target which can be used for a future attack.
This is often publicly available information.
ie. nslookup to learn an ip address of a site

Malware (malicious software) refers to a variety of harmful programs that can infect a computer.
Viruses infect other software (a 'host program'). The virus spreads as the software is shared by users. Typically they corrupt or modify file on the target computer.
Worms do not require a host program. They are standalone malware and they are able to spread on their own, without user interaction. The spread of worms can congest the network, but the
'payload' of a worm can cause additional harm to target devices.
Trojan Horses are harmful software that is disguised as legitimate software. They are spread through user interaction such as opening email attachments, or downloading a file from the Internet.

Social engineering attacks target the most vulnerable part of any system - people!
They involve psychological manipulation to make the target reveal confidential information or perform some action.
Phishing typically involves fradulent emails that appear to come from a legitimate business (Amaon, bank, credit card company, etc.) and contain links to a fraudulent website that seems legitimate.
  spear phishing is a more targeted form of phishing, ie. aimed at employees of a certain company.
  whaling is phishing targeted at high-profile individuals, ie. a company president.
Vishing (voice phishing) is phishing performed over the phone.
Smishing (SMS phishing) is phishing using SMS text messages.
Watering hole attacks compromise sites that the target victim frequently vists. If a malicious link is placed on a website the target trusts, theymight not hesitate to click it.
Tailgating attacks involve entering restricted, secured areas by simply walking in behind an authorized person as they enter.

Attakers can learn a user's passwords via multiple methods:
  Guessing
  Dictionary attack: A program runs through a 'dictionary' or list of common words/passwords to find the target's password
  Brute force attack: A program tries every possible combination of letters, numbers, and special characters to find the target's password.
  
Multi-factor authentication involves providing more than just a username/password to prove your identity.
Providing two of the following:
  Something you know
  Something you have
  Something you are
Digital certificates are another form of authentication used to prove the identity of the holder of the certificate.
They are used for websites to verify that the website being accessed is legitmate.
Entities that want a certificate to prove their identity send a CSR (Certificate Signing Request) to a CA (Certificate Authority), which will generate and sign the certificate.

AAA stands for Authentication, Authorization, and Accounting.
It is a framework for controlling and monitor users of a computer system (ie. a network)
Authentication is the process of verifying a user's identity.
Authorization is the process of granting the user the appropriate access and permissions.
Accounting is the process of recording the user's activities on the system.
Enterprises typically use a AAA server to provide AAA services.
AAA servers usually support the following two AAA protocols:
  RADIUS: an open standard protocol. Uses UDP ports 1812 and 1813.
  TACACS+: A Cisco propriety protocol. Uses TCP port 49.

User awareness programs are designed to make employees aware of potential security threats and risks. 
User training programs are more formal than user awareness programs.
Physical access control protects equipment and data from potential attackers by only allowing authorized users into protocted areas such as network closets or data cneter floors.

Port security is a security feature of Cisco switches.
It allows you to control which source MAC addresses ar eallowed to enter the switchport.
If an unauthorized source MAC address enters the port, an action will be taken.
  The default action is to place the interface in an 'err-disabled' state.
  If you don't configure it manually, the switch will allow the first source MAC address that enters the interface.
You can change the maximum number of MAC addresses allowed.
A combination of manually configured MAC addresses and dynamically learned addresses is possible.

Port security allows network admins to control which devices are allowed to access the network.
However, MAC address spoofing is a simple task.
  It's easy to configure a device to send frames with a different source MAC address.
Rather than manually specifying the MAC addresses allowed on each port, port security's ability to limit the number of MAC addresses allowed on an interface is more useful.

Port security can be enabled on access ports or trunks ports, but they must be statically configured as access or trunk.
*switchport port-security*
*show port-security interface <int>*

Use *shutdown*, then *no shutdown* commands to re-enable an interface.

*show errdisable recover*
Every 5 minutes (by default), all err-disabled interfaces will be re-enabled if err-disable recovery has been enabled for the cause of the interface's disablement.

*errdisable recovery cause psecure-violation*
*errdisable recovery interval 180*

ErrDisable Recovery is useless if you don't remove the device that caused the interface to enter the err-disabled state!

There are three different violation modes that determine what the switch will do if an unauthorized frame enters an interface configured with port security.
Shutdown
  Effectively shuts down the port by placing it in an err-disabled state.
  Generates a Syslog and/or SNMP message when the interface is disabled.
  The violation counter is set to 1 when the interface is disabled.
Restrict
  The switch discards traffic from unauthorized MAC addresses
  The interface is NOT disabled
  Generates a Syslog and/or SNMP message each time an unauthorized MAC is detected.
  The violation counter is incremented by 1 for each unauthorized frame
Protect
  The switch discards traffic from unauthorized MAC addresses
  The interface is NOT disabled
  It does NOT generate Syslog/SNMP messages for unauthorized traffic
  It does NOT increment the violation counter

*switchport port-security violation restrict*

*switchport port-security violation protect*

By default secure MAC addresses will not 'age out'
  Can be configure with *switchport port-security aging time <min>*
The default aging type is Absolute
  Absolute: After the secure MAC address is learned, the aging timer starts and the MAc is removed after the timer expires, even if the switch continures receiving frames from that source MAC address.
  Inactivity: After the secure MAC address is learned, the aging timer starts but is reset every time a frame from that source MAC address is received on the interface.
  Aging type is configured with *switchport port-security aging type {absolute | inactivity}
Secure Static MAC aging (addresses configured with *switchport port-security mac-address <mac>* is disabled by default.
  Can be enabled with *switchport port-security aging static*

*show port-security*

'Sticky' secure MAC address learning can be enabled with the following command: *switchport port-security mac-address sticky*
When enabled, dynamically-learned secure MAC addresses will be added to the running config like this: *swtichport port-security mac-address sticky <mac>*
The 'sticky' secure MAC addresses will never age out.
  You need to save the running-config to the startup-config to make them truly permanent
When you issue the *switchport port-security mac-address sticky* command, all current dynamically-learned secure MAC addresses will be converted to sticky secure MAC address.
If you issue the "no switchport port-security mac-address sticky* command, all current sticky secure MAC addresses will be converted to regular dynamically-learned secure MAC addresses.

Secure MAC addresses will be added to the MAC address talbe like any other MAC address.
  Sticky and Static secure MAC addresses will have a type of STATIC
  Dynamically-learned secure MAC addresses will have a type of DYNAMIC
  You can view all secure MAC addresses with *show mac address-table secure*
  






































  












