---
title: Communication Over the Network
description: A compilation of notes for networking fundamentals
date: 2025-07-24 17:55:00 +1000
categories: [STUDY_NOTES, networking]
tags: [networking]
---

# Communication Over the Network

## 1. Message Communication Fundamentals

### Basic Communication Model

Every communication system requires three essential components:

- **Sender:** The source device initiating communication
- **Receiver:** The destination device receiving the message
- **Channel/Medium:** The transmission path between sender and receiver

### Message Segmentation

#### Why Segment Messages?

Large messages create significant problems in network communication:

- **Resource Monopolisation:** Single large message blocks network resources
- **Reduced Efficiency:** Other communications must wait
- **Reliability Issues:** If transmission fails, entire message must be resent

#### Segmentation Benefits

- **Improved Reliability:** Failed segments can be re-transmitted individually
- **Multiplexing Support:** Multiple conversations can occur simultaneously
- **Better Resource Utilisation:** Network bandwidth shared more efficiently

#### Segmentation Challenges

- **Increased Complexity:** Each segment must be tracked and sequenced
- **Reassembly Requirements:** Destination must reconstruct original message
- **Additional Overhead:** Headers and control information added to each segment

## 2. Network Components

### Network Building Blocks

#### End Devices (Hosts)

Devices that originate or receive network communications:

- **Personal Devices:** PCs, smartphones, tablets
- **Infrastructure Devices:** Servers, printers, IP phones
- **IoT Devices:** Smart home devices, sensors, cameras
- **Role:** Source or destination of data communications

#### Intermediary Devices

Devices that forward and manage network traffic:

- **Routers:** Connect different networks, make routing decisions
- **Switches:** Connect devices within the same network
- **Access Points:** Provide wireless connectivity
- **Firewalls:** Provide security filtering

#### Intermediary Device Functions

1. **Signal Regeneration:** Amplify and clean signals for long-distance transmission
2. **Routing Decisions:** Determine best path for data forwarding
3. **Security Enforcement:** Apply access control and filtering policies
4. **Quality of Service (QoS):** Prioritize different types of traffic
5. **Error Notification:** Report transmission problems
6. **Failover Support:** Provide alternate paths when primary routes fail

### Network Media Types

#### Media Selection Process

Networks convert data into transmittable signals through **encoding**:

- **Electrical signals** for copper media
- **Light pulses** for fiber-optic media
- **Electromagnetic waves** for wireless media

#### Copper Media

Most common and cost-effective transmission medium:

**Unshielded Twisted Pair (UTP)**

- **Usage:** Standard for LANs and telephone systems
- **Advantages:** Low cost, easy installation, widely supported
- **Limitations:** Distance restrictions, electromagnetic interference susceptibility

**Shielded Twisted Pair (STP)**

- **Usage:** Environments with high electromagnetic interference
- **Advantages:** Better noise protection than UTP
- **Disadvantages:** Higher cost, more complex installation

**Coaxial Cable**

- **Usage:** Legacy networks, cable TV, some WAN connections
- **Characteristics:** Better shielding than twisted pair, longer distances
- **Current Role:** Mostly replaced by UTP and fiber

#### Fiber-Optic Media

High-performance transmission using light signals:

**Advantages:**

- **High Bandwidth:** Supports very high data rates
- **Low Latency:** Light travels at near light-speed
- **Long Distance:** Can transmit over many kilometers
- **Immunity:** Not affected by electromagnetic interference
- **Security:** Difficult to tap without detection

**Applications:**

- **Enterprise Backbones:** High-speed building interconnections
- **Long-Haul Networks:** Internet backbone connections
- **Fiber to the Home (FTTH):** Residential broadband services
- **Submarine Cables:** Intercontinental communications

#### Wireless Media

Uses radio frequency spectrum for communication:

**Advantages:**

- **Mobility:** Users can move while maintaining connectivity
- **Flexibility:** Easy to add new devices
- **Cost-Effective:** No cable installation in some scenarios

**Disadvantages:**

- **Interference:** Subject to electromagnetic interference
- **Security Risks:** Signals can be intercepted more easily
- **Capacity Limitations:** Shared medium with finite spectrum
- **Weather Sensitivity:** Some frequencies affected by atmospheric conditions

#### Media Selection Criteria

- **Distance Requirements:** How far signals must travel
- **Environmental Factors:** Indoor/outdoor, interference levels
- **Bandwidth Needs:** Required data transmission speeds
- **Cost Constraints:** Budget for installation and maintenance
- **Future Scalability:** Ability to support growth

## 3. Network Classifications

### Size-Based Network Types

#### Local Area Network (LAN)

- **Scope:** Single building or campus
- **Characteristics:** High bandwidth, low error rates, single organization control
- **Technologies:** Ethernet, Wi-Fi
- **Typical Speed:** 100 Mbps to 10+ Gbps

#### Wide Area Network (WAN)

- **Scope:** Large geographic areas, often global
- **Characteristics:** Lower speeds than LAN, higher error rates, multiple carrier involvement
- **Technologies:** MPLS, Internet, satellite links
- **Typical Speed:** Variable, from T1 (1.5 Mbps) to 100+ Gbps

#### Specialized Network Types

**Metropolitan Area Network (MAN)**

- **Scope:** City or metropolitan area
- **Usage:** Connecting multiple LANs within a city

**Wireless LAN (WLAN)**

- **Scope:** LAN using wireless technology
- **Standards:** IEEE 802.11 (Wi-Fi)

**Storage Area Network (SAN)**

- **Purpose:** Dedicated network for storage systems
- **Features:**
    - High reliability and performance
    - Low latency for storage access
    - Block-level storage access
    - Typically uses Fibre Channel or iSCSI protocols

## 4. Network Protocols

### Protocol Fundamentals

**Protocol:** A set of rules governing communication between network devices **Protocol Suite:** A group of interrelated protocols working together (e.g., TCP/IP)

### Protocol Implementation

- **Software:** Operating system network stacks
- **Hardware:** Network interface cards, routers, switches
- **Firmware:** Embedded protocol implementations

### Protocol Categories by Layer

#### Application Layer Protocols

- **HTTP/HTTPS:** Web browsing and services
- **SMTP/POP3/IMAP:** Email services
- **FTP/SFTP:** File transfer
- **DNS:** Domain name resolution
- **DHCP:** Automatic IP address assignment

#### Transport Layer Protocols

- **TCP:** Reliable, connection-oriented communication
- **UDP:** Fast, connectionless communication

#### Network Layer Protocols

- **IP (IPv4/IPv6):** Logical addressing and routing
- **ICMP:** Error reporting and network diagnostics
- **OSPF/EIGRP:** Routing protocols

#### Data Link Layer Protocols

- **Ethernet:** Wired LAN standard
- **Wi-Fi (802.11):** Wireless LAN standard
- **PPP:** Point-to-point connections

### Standards Organizations

#### Proprietary vs. Open Standards

**Proprietary Standards:**

- Developed by individual companies
- Often provide competitive advantages
- May limit interoperability

**Open Standards:**

- Developed by industry organizations
- Promote interoperability
- Enable competitive marketplace

#### Key Standards Bodies

- **IEEE:** Institute of Electrical and Electronics Engineers
- **IETF:** Internet Engineering Task Force
- **ITU:** International Telecommunication Union
- **ISO:** International Organization for Standardization

## 5. Network Models

### Purpose of Layered Models

Network models provide structured approaches to understanding complex communications:

#### Benefits of Layering

1. **Design Simplification:** Complex processes broken into manageable layers
2. **Interoperability:** Different vendors can implement compatible solutions
3. **Change Isolation:** Modifications in one layer don't affect others
4. **Consistent Terminology:** Common language for network professionals
5. **Troubleshooting:** Systematic approach to problem isolation

#### Model Types

**Protocol Models:** Describe specific protocol suites (e.g., TCP/IP model) **Reference Models:** Provide generic frameworks (e.g., OSI model)

### OSI Reference Model

The Open Systems Interconnection model defines seven distinct layers:

|Layer|Name|Primary Functions|Examples|
|---|---|---|---|
|7|Application|User interface, file access, network services|HTTP, SMTP, FTP|
|6|Presentation|Data formatting, encryption, compression|SSL/TLS, JPEG, GIF|
|5|Session|Session establishment, management, termination|SQL sessions, RPC|
|4|Transport|Reliable delivery, flow control, segmentation|TCP, UDP|
|3|Network|Logical addressing, routing, path determination|IP, ICMP, OSPF|
|2|Data Link|Frame creation, error detection, MAC addressing|Ethernet, Wi-Fi|
|1|Physical|Bit transmission, electrical/optical signaling|Cables, connectors, hubs|

#### Layer Details

**Layer 7 - Application**

- Provides network services directly to user applications
- Handles file transfers, email, web browsing
- Interface between user and network

**Layer 6 - Presentation**

- Data translation, encryption, and compression
- Ensures data from sender's application layer can be read by receiver
- Character encoding, data compression

**Layer 5 - Session**

- Establishes, manages, and terminates sessions
- Provides synchronization and checkpoint services
- Handles full-duplex, half-duplex, or simplex operations

**Layer 4 - Transport**

- End-to-end delivery and error recovery
- Segmentation and reassembly of data
- Flow control and congestion management

**Layer 3 - Network**

- Logical addressing using IP addresses
- Routing between different networks
- Path determination and packet forwarding

**Layer 2 - Data Link**

- Node-to-node delivery within same network
- Error detection and correction
- MAC addressing for local delivery

**Layer 1 - Physical**

- Actual transmission of raw bits
- Defines electrical, mechanical, and procedural specifications
- Cables, connectors, voltage levels, timing

## 6. Communication Process & Data Encapsulation

### Data Flow Process

1. **Data Creation:** Application generates data to send
2. **Encapsulation:** Each layer adds its own header (and sometimes trailer)
3. **Transmission:** Physical layer converts to signals and transmits
4. **Reception:** Receiving device captures signals
5. **Decapsulation:** Each layer removes its header and processes data
6. **Data Delivery:** Application receives original data

### Protocol Data Units (PDUs)

Each layer creates its own data structure:

|Layer|PDU Name|Components|
|---|---|---|
|Application (7-5)|Data|User data|
|Transport (4)|Segment|Transport header + Data|
|Network (3)|Packet|Network header + Segment|
|Data Link (2)|Frame|Data link header + Packet + Trailer|
|Physical (1)|Bits|Electrical/optical signals|

### Encapsulation Process

**Encapsulation:** Process of wrapping data with protocol information

- Each layer adds its own header
- Headers contain control information for that layer
- Some layers also add trailers (e.g., Data Link layer)

**Decapsulation:** Reverse process at receiving end

- Each layer removes and processes its header
- Data is passed up to the next layer
- Original data reconstructed at application layer

## 7. Network Addressing

### Multi-Layer Addressing System

Networks use different addressing schemes at different layers:

#### Layer 2 - Data Link Addressing

**MAC (Media Access Control) Address:**

- **Purpose:** Identifies devices within the same network segment
- **Format:** 48-bit hexadecimal address (e.g., 00:1A:2B:3C:4D:5E)
- **Scope:** Local network only
- **Assignment:** Burned into network interface cards by manufacturers
- **Uniqueness:** Globally unique identifier

#### Layer 3 - Network Addressing

**IP Address:**

- **Purpose:** Enables communication across different networks
- **IPv4 Format:** 32-bit dotted decimal (e.g., 192.168.1.100)
- **IPv6 Format:** 128-bit hexadecimal (e.g., 2001:db8::1)
- **Scope:** Global internetwork
- **Assignment:** Configured by network administrators or DHCP
- **Routing:** Used by routers to forward packets between networks

#### Layer 4 - Port Addressing

**Port Numbers:**

- **Purpose:** Identify specific applications or services
- **Range:** 0-65535
- **Categories:**
    - **Well-known ports (0-1023):** Reserved for system services
    - **Registered ports (1024-49151):** Assigned to applications
    - **Dynamic ports (49152-65535):** Used for temporary connections
- **Examples:** HTTP (80), HTTPS (443), SMTP (25), DNS (53)

### Address Resolution Process

**ARP (Address Resolution Protocol):**

- Maps IP addresses to MAC addresses
- Required when devices need to communicate on same network
- Maintains ARP cache for efficiency
- Uses broadcast for unknown addresses

---

## Key Concepts Summary

### Network Communication Principles

- All communication requires sender, receiver, and medium
- Message segmentation improves efficiency and reliability
- Multiple addressing layers enable scalable networking

### Infrastructure Components

- End devices generate and consume data
- Intermediary devices forward and manage traffic
- Media selection depends on distance, speed, cost, and environment

### Protocol Organization

- Layered models simplify complex communications
- Each layer has specific responsibilities
- Encapsulation enables multi-layer functionality
- Standards ensure interoperability
