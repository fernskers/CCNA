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














