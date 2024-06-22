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
Traffic using the EtherChannel will be load balanced among the physical interfaces in the group. An algorithm is used to determine which traffic will use which
physical interface.
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
*port-channel load-balance <method>* (change load-balance config)

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





















