What is a network? A system of interconnected entities, such as computers, servers, routers, etc. that are linked together to share resources,
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

interface <interface> = enter interface configuration mode
ip address <ip address> <mask> = assign ip address and subnet mask
no shutdown = enable interface
show interfaces = displays information about interfaces

hostname <name> = configure's devices hostname
do show ip interface brief = view interfaces/ip addresses/status/protocol

show interfaces status = shows port/name/status/vlan/duplex/speed type

interface range f0/x - y (easy way to configure multiple interfaces)

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

IPv4 Header Fields

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

Routing Fundamentals

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

ip route <ip-address> <netmask> <next-hop>
ip route <ip-address> <netmask> <exit-interface>

A default route is a route to 0.0.0.0/0 is the least specific route possible; it includes every possible destination IP address.
If the router doesn't have any more specific routes that match a packet's destination IP address, the router will forward the packet using the default route.
A default route is often used to direct traffic to the Internet. More specific routes are used for destinations in the internal corporate network.
Traffic to destinations outside of the internal network is sent to the Internet.

Only class A, B, C can be assigned to a device as an IP address.

The IANA (Internet Assigned Numbers Authority) assigns IPv4 addresses/networks to companies based on their size.
For example, a very large company might receive a class A or class B network, while a small company might receive a class C network.
However, this led to many wasted IP addresses.


























































