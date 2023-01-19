---
title: Computer Networks Notes
tags: [Computer Science, Computer Networks]
style: fill
color: secondary
description: Basic concepts for understanding the computer networks.
---

## Introduction

A computer network is a system that connects scattered computer systems with independent functions through communication equipment and lines and realizes resource sharing and information transmission by software with complete functions. Roughly speaking, networks can be divided into LANs, MANs, WANs, and internetworks.
- LANs (local area network) typical cover a building and operate at high speeds. 
- MANs (metropolitan area network) usually cover a city. An example is the cable television system, which is now used by many people to access the Internet.
- WANs (wide area network) may cover a country or a continent. Some of the technologies used to build these networks are point-to-point (e.g., a cable) while others are broadcast (e.g.,wireless). 

Network software is built around protocols, which are rules by which processes communicate. Most networks support protocol hierarchies, with each layer providing services to the layer above it and insulating them from the details of the
protocols used in the lower layers. Protocol stacks are typically based either on the OSI model or on the TCP/IP model. Both have the link, network, transport, and application layers, but they differ on the other layers. Design issues include reliability, resource allocation, growth, security, and more.

![OSI TCP/IP]({{site.baseurl}}/assets/NetworkNotes/OSITCPIP.png)

![OSI]({{site.baseurl}}/assets/NetworkNotes/OSI.jpeg)

## Physical Layer

The physical layer is the basis of all networks. Nature imposes two fundamental limits on all channels, and these determine their bandwidth. These limits are the **Nyquist limit**, which deals with noiseless channels, and the **Shannon limit**, which deals with noisy channels.

- Noiseless Channel : Nyquist Bit Rate

    BitRate = 2 * Bandwidth * log2(L)

    where L is the number of signal levels used to represent data.

- Noisy Channel : Shannon Capacity

    Capacity = bandwidth * log2(1 + SNR)

    where, SNR is the signal-to-noise ratio


Transmission Modes:
- Simplex Mode: the communication is unidirectional, as on a one-way street.Only one of the two devices on a link can transmit, the other can only receive.
- Half-duplex Mode: each station can both transmit and receive, but not at the same time.
- Full-duplex Mode: both stations can transmit and receive simultaneously.

Manchester Encoding: When there is a long sequence of 0s and 1s, there is a problem at the receiving end. The problem is that the synchronization is lost due to lack of transmissions.
- NRZ-level encoding : The polarity of signals changes when incoming signal changes from ‘1’ to ‘0’ or from ‘0’ to ‘1’. It considers the first bit data as polarity change.
- NRZ-Inverted/ Differential encoding:In this, the transitions at the beginning of bit interval is equal to 1 and if there is no transition at the beginning of bit interval is equal to 0.

Transmission media can be guided or unguided. The principal guided media are twisted pair, coaxial cable, and fiber optics. Unguided media include terrestrial radio, microwaves, infrared, lasers through the air, and satellite.

## Data Link Layer

The task of the data link layer is to convert the raw bit stream offered by the physical layer into a stream of frames for use by the network layer. The link layer can present this stream with varying levels of reliability, ranging from connectionless, unacknowledged service to reliable, connection-oriented service.

### Framing

It provides a way for a sender to transmit a set of bits that are meaningful to the receiver. 
- Character/Byte Stuffing: Used when frames consist of character. If data contains ED then, byte is stuffed into data to differentiate it from ED.
- Bit stuffing: Sender stuffs a bit to break the pattern i.e. here appends a 0 in data = 011101.

### Error Control

Network designers have developed two basic strategies for dealing with errors. Both add redundant information to the data that is sent.  

- Error-correcting codes: include enough redundant information to enable the receiver to deduce what the transmitted data must have been. 
    1. Hamming codes.
    2. Binary convolutional codes.
    3. Reed-Solomon codes.
    4. Low-Density Parity Check codes.

- Error-detecting codes: other is to include only enough redundancy to allow the receiver to deduce that an error has occurred (but not which error)
    1. Parity.
    2. Checksums.
    3. Cyclic Redundancy Checks (CRCs).

### Flow Control

Flow control is to match the sending speed and receiving speed during the transmission process, reducing transmission errors and resource waste.

![Flow Control]({{site.baseurl}}/assets/NetworkNotes/FlowControl.jpeg)

### Media Access Control

Channelization Protocol: FDMA; TDMA; CDMA; WDMA.

Random Access Protocol
- ALOHA
- CSMA (carrier sense multiple access)
- CSMA/CD (carrier sense multiple access with collision detection)
- CSMA/CA (carrier sense multiple access with collision avoidance)

Those carrier sensing techniques have led to a variety of CSMA protocols for LANs and MANs. It is the basis for classic Ethernet and 802.11 networks. In the dominant wireless LAN, **IEEE 802.11**, stations use CSMA/CA to mitigate the first problem by leaving small gaps to avoid collisions.

Controlled Access Protocol
- Reservation
- Rolling
- Token Passing

**Ethernet** is the dominant form of wired LAN. Classic Ethernet used CSMA/CD for channel allocation on a yellow cable the size of a garden hose that snaked from machine to machine. The architecture has changed as speeds have risen from 10 Mbps to 10 Gbps and continue to climb. Now, point-to-point links such as twisted pair are attached to hubs and switches. With modern switches and full-duplex links, there is no contention on the links and the switch can forward frames between different ports in parallel.

## Network Layer

The network layer provides services to the transport layer. It can be based on either datagrams or virtual circuits. In both cases, its main job is routing packets from the source to the destination. In datagram networks, a routing decision is made on every packet. In virtual-circuit networks, it is made when the virtual circuit is set up.

The glue that holds the whole Internet together is the network layer protocol, **IP (Internet Protocol)**. Communication in the Internet works as follows. The transport layer takes data streams and breaks them up so that they may be sent as IP packets. In theory, packets can be up to 64 KB each, but in practice they are usually not more than 1500 bytes (so they fit in one Ethernet frame). IP routers forward each packet through the Internet, along a path from one router to the next, until the destination
is reached. At the destination, the network layer hands the data to the transport layer, which gives it to the receiving process. When all the pieces finally get to the destination machine, they are reassembled by the network layer into the original datagram.

The IPv4 (Internet Protocol) header

![IPV4]({{site.baseurl}}/assets/NetworkNotes/IPV4.png)

IP address formats

![IP address formats]({{site.baseurl}}/assets/NetworkNotes/IPaddressformats.jpeg)

The IPv6 header

![IPV6]({{site.baseurl}}/assets/NetworkNotes/IPV6.png)

**Internet Control Message Protocol (ICMP)**: Since IP does not have a inbuilt mechanism for sending error and control messages. It depends on Internet Control Message Protocol(ICMP) to provide an error control.
1. Source quench message
2. Parameter problem
3. Time exceeded message
4. Destination un-reachable

The main routing protocols used within networks are OSPF and RIP, and the routing protocol used across networks is BGP.

**Open shortest path first (OSPF)** uses a **Link State Routing (LSR)** algorithm and falls into the group of interior gateway protocols (IGPs), operating within a single autonomous system (AS).

OSPF gathers link state information from available routers and constructs a topology map of the network. The topology is presented as a routing table to the Internet Layer for routing packets by their destination IP address. OSPF supports Internet Protocol Version 4 (IPv4) and Internet Protocol Version 6 (IPv6) networks and supports the Classless Inter-Domain Routing (CIDR) addressing model.

**Routing Information Protocol(RIP)** is a dynamic routing protocol which uses hop count as a routing metric to find the best path between the source and the destination network. It is a **Distance Vector Routing (DVR)** protocol which has AD value 120 and works on the application layer of OSI model. RIP uses port number 520.

Hop Count:
1. Hop count is the number of routers occurring in between the source and destination network. The path with the lowest hop count is considered as the best route to reach a network and therefore placed in the routing table.
2. The maximum hop count allowed for RIP is 15 and hop count of 16 is considered as network unreachable.

**Border gateway protocol (BGP)**: is a standardized exterior gateway protocol designed to exchange routing and reachability information among autonomous systems (AS) on the Internet. BGP is classified as a path-vector routing protocol, and it makes routing decisions based on paths, network policies, or rule-sets configured by a network administrator.

| Attribute | OSPF | RIP | BGP |  
|:---:|:---:|:---:|:---:|
| Protocols | IP | UDP | TCP|
| Types | Within AS | Within AS | Between AS | 
| Convergence | Fast | Slow | Slow |  
| Network size | For large and small networks. | Only for small to medium networks due to the fact that max. metric is 15 hops. | For large networks. |  
| Need of device resources | Memory and CPU intensive. | Much less memory and CPU intensive than OSPF. | Depends on the size of the routing table but scales better than OSPF. |  
| Need of network resources | Less than RIP; only small updates are sent. | Bandwidth consuming; whole Routing table is sent (default: every 90 seconds). | Bandwidth consuming while learning network routes from connected AS or while update bursts. |  
| Metric | Is based on bandwidth. | Is based on hop count, no matter how fast the connections are. | Is based on AS Path, IGP-Metric, Multi-Exit Discriminator, Communities, Local Preferences, Next Hop, Weight and Origin. |  
| Design | Hierarchical network possible. | Flat network. | Fully meshed. |  

## Transport Layer

Transport layer provides efficient, reliable, and cost-effective data transmission service to its users, normally **processes** in the application layer.

**Together with the network layer, transport layer is the heart of the protocol hierarchy.**

Even when the network layer is completely reliable, the transport layer has plenty of work to do. It must handle all the service primitives, manage connections and timers, allocate bandwidth with congestion control, and run a variable-sized sliding window for flow control.

The Internet has two main transport protocols: UDP and TCP. 

- UDP is a connectionless protocol that is mainly a wrapper for IP packets with additional address. UDP can be used for client-server interactions, for example, using RPC. It can also be used for building real-time protocols such as RTP. The UDP header is shown in the image below.

![UDP Header]({{site.baseurl}}/assets/NetworkNotes/UDPHeader.png)

- The main Internet transport protocol is TCP. It provides a reliable, bidirectional, congestion-controlled byte stream with a 20-byte header on all segments. A great deal of work has gone into optimizing TCP performance, using algorithms from Nagle, Clark, Jacobson, Karn, and others. The TCP header is shown in the image below.

![TCP Header]({{site.baseurl}}/assets/NetworkNotes/TCPHeader.png)

TCP provides reliable communication with something called Positive Acknowledgement with Re-transmission(PAR). The Protocol Data Unit(PDU) of the transport layer is called a segment. A TCP connection is established with a 3-Way Handshake Process:

- **Step 1 (SYN)**: In the first step, client wants to establish a connection with server, so it sends a segment with SYN(Synchronize Sequence Number) which informs server that client is likely to start communication and with what sequence number it starts segments with
- **Step 2 (SYN + ACK)**: Server responds to the client request with SYN-ACK signal bits set. Acknowledgement(ACK) signifies the response of segment it received and SYN signifies with what sequence number it is likely to start the segments with
- **Step 3 (ACK)**: In the final part client acknowledges the response of server and they both establish a reliable connection with which they will start eh actual data transfer.

![TCP Handshake Process]({{site.baseurl}}/assets/NetworkNotes/TCPHandshakeProcess.png)

TCP uses a congestion window and a congestion policy that avoid congestion. 

Congestion policy in TCP:
1. **Slow Start Phase: exponential increment** - starts slowly increment is exponential to threshold.
2. **Congestion Avoidance Phase: additive increment** - After reaching the threshold increment is by 1.
3. **Congestion Detection Phase: multiplicative decrement**- Sender goes back to Slow start phase or Congestion avoidance phase.
    - **Case 1 : Retransmission due to Timeout** – In this case congestion possibility is high.
    - **Case 2 : Retransmission due to 3 Acknowledgement Duplicates** – In this case congestion possibility is less.

## Application Layer

The layers below the application layer are there to provide transport services, but they do not do real work for users. In the application layer, different protocols are provided to allow the applications to function. 

**Client/Server Model**: In the Client/Server (C/S) model, there is one always-on host called the server, which services requests from many other hosts called clients. Its workflow is as follows:
1. The server is in the state of receiving the request.
2. The client sends out a service request and waits for the result to be received.
3. After receiving the request, the server analyzes the request, performs necessary processing, obtains the result and sends it to the client.

**Peer-to-Peer Model (P2P)**: In the P2P model, each computer has no fixed client and server division. Instead, any pair of computers, called peers, communicate directly with each other. In fact, the P2P model still uses the client/server model in essence, and each node not only accesses the resources of other nodes as a client but also provides resources for other nodes to access as a server.

### Domain Name Server

DNS is a host name to IP address translation service. DNS is a distributed database implemented in a hierarchy of name servers. It is an application layer protocol for message exchange between clients and servers. At the top level are the well-known generic domains, including com and edu, as well as about 200 country domains. DNS is implemented as a distributed database with servers all over the world. By querying a DNS server, a process can  **map an Internet domain name onto the IP address** used to communicate with a computer for that domain.

### Dynamic Host Configuration Protocol(DHCP) 

DHCP is an application layer protocol which is used to provide: 

1. Subnet Mask (Option 1 – e.g., 255.255.255.0)
2. Router Address (Option 3 – e.g., 192.168.1.1)
3. DNS Address (Option 6 – e.g., 8.8.8.8)
4. Vendor Class Identifier (Option 43 – e.g., ‘unifi’ = 192.168.1.9 ##where unifi = controller)

DHCP is based on a client-server model and based on discovery, offer, request, and ACK. 

DHCP port number for server is 67 and for the client is 68. It is a Client server protocol which uses UDP services. IP address is assigned from a pool of addresses. In DHCP, the client and the server exchange mainly 4 DHCP messages in order to make a connection, also called DORA process, but there are 8 DHCP messages in the process. 

### Email

Email is the original killer app of the Internet. It is still widely used by everyone from small children to grandparents. Most email systems in the world use the mail system now defined in RFCs 5321 (SMTP) and 5322 (IMF). Messages have simple ASCII headers, and many kinds of content can be sent using MIME. Mail is submitted to message transfer agents for delivery and retrieved from them for presentation by a variety of user agents, including Web applications. Submitted mail is delivered using SMTP, which works by making a TCP connection from the sending message transfer agent to the receiving one.

### File Transfer Protocol (FTP)

File Transfer Protocol(FTP) is an application layer protocol which moves files between local and remote file systems. It runs on the top of TCP, like HTTP. To transfer a file, 2 TCP connections are used by FTP in parallel: control connection and data connection.


### The Hypertext Transfer Protocol (HTTP)

The Hypertext Transfer Protocol (HTTP) is an application-level protocol that uses TCP as an underlying transport and typically runs on port 80. HTTP is a stateless protocol i.e. server maintains no information about past client requests.

HTTP Connections
- Non-Persistent
- Persistent

Nowadays, much of the content on the Web is produced dynamically, either at the server (e.g., with PHP) or in the browser (e.g., with JavaScript). When combined with back-end databases, dynamic server pages allow Web applications such as e-commerce and search. 


## Reference

1. Computer Networks (5th Edition)
2. [https://www.geeksforgeeks.org/last-minute-notes-computer-network/](https://www.geeksforgeeks.org/last-minute-notes-computer-network/)

