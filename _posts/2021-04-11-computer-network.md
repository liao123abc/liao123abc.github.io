---
layout: post
title: "computer-network"
subtitle: "computer-network"
date: 2021-04-11
author: "thomasliao"
header-img: "img/post-bg-2015.jpg"
tags:
    - computer network
    - note
    - Anki
---

# computer networks


##  1.2 network hardware
- taxonomy of computer networks
    - transmission technology
        - broadcast links
            - A wireless network
        - broadcasting: every machine
        - multicasting: subset of the machines
        - point-to-point links, sometimes called unicasting
    - scale
        - PANs (Personal Area Networks)
            - Bluetooth
                - The master tells the slaves 
                    - what addresses to use, 
                    - when they can broadcast, 
                    - how long they can transmit, 
                    - what frequencies they can use,and so on
        - LAN (Local Area Network)
            - a standard for wireless LANs called IEEE 802.11, popularly known as WiFi
            - IEEE 802.3, popularly called Ethernet, is, by far, the most common type of wired LAN.
        - MAN (Metropolitan Area Network)
        - WAN (Wide Area Network)
        - Internetworks
            -  A collection of interconnected networks is called an internetwork or internet.
            - worldwide Internet (is one specific internet)


## 1.3.1 Protocol Hierarchies
- each layer passes data and control information to the layer immediately below it, until the lowest layer is reached.
- It is common that different hosts use different implementations of the same protocol 
- the lower layers of a protocol hierarchy are frequently implemented in hardware or firmware

## 1.3.2 Design Issues for the Layers

### Reliability 
- bits are damaged
    - errors:
        - fluke electrical noise, 
        - random wireless signals, 
        - hardware flaws, 
        - software bugs and so on
    - solution: adding redundant information, used at low layers, to protect packets sent over individual links,and high layers, to check that the right contents were received
        - error detection
            - Information that is incorrectly received can then be retransmitted, until it is received correctly
        - error correction
            - where the correct message is recovered from the possibly incorrect bits that were originally received
- finding a working path through a network
    - may be some links or routers that are broken
    - routing: The network should automatically make this decision

### the evolution of the network
- protocol layering
    - internetworking, different network technologies often have different limitations .ex:
        - number messages
            - not all communication channels preserve the order of messages sent on them
        - differences in the maximum size of a message that the networks can transmit
            - disassembling, transmitting, and then reassembling messages
    - traffic jams

### resource allocation
- statistical multiplexing
    - share the bandwith dynamically, rather than by giving each host a fixed fraction
    - sharing based on the statistics of demand
- how to keep a fast sender from swamping a slow receiver with data
    - flow control: feedback from the receiver
- congestion: network is oversubscribed, overloading
    - too many computers want to send too much traffic
    - the network cannot deliver it all
- resource
    - bandwidth
    - timeliness
        - live video
        - real-time

### security
- eavesdropping[ secretly listening to the private conversation]
    - solution: provide confidentiality
        - authentication: prevent someone from impersonating someone else
        - integrity: prevent surreptitious changes to messages

## 1.3.3 Connection-Oriented Versus Connectionless Service

### Connection-oriented
- Connection-oriented service is modeled after the telephone system.
    - establish
    - use
    - release
- service is modeled after the telephone system.
- A circuit is another name for a connection with associated resources
    - fixed bandwidth
    - in some case negotiation parameters
        - maximum message size
        - quality of service required
        - one side make proposal, the other accept||reject||make a counterproposal
    - acts like a tube: 
        - sender pushes at one end , receiver takes them at the other end
        - in most case the order is preserved: bits arrive in the order they were sent

### Connectionless Service
- connectionless service is modeled after the postal system
    - each msg carries the full destination address
    - a packet is a message at the network layer
    - store-and-forward switching： 
        - nodes receive a message in full before sending it to the next
    - cut-through switching:
        - onward transmission of a message at a node starts before it is completely received
    - order is not consistant because of delayed

### reliability
> both Connection-Oriented or Connectionless Service  can further be characterized by its reliability
- implemented by having the receiver acknowledge the receipt of each msg
- Reliable connection-oriented service has two minor variations: 
    - message sequences
        - message boundaries are preserved
    - byte streams
        - no message boundaries
- Unreliable (meaning not acknowledged) connectionless service is often called datagram service
    - does not return an acknowledgement to the sender
    - dominant form in most networks
        - reliable communication (in our sense, that is,acknowledged) may not be available in a given layer
            - Ethernet does not provide reliable communication
            - many reliable services are built on top of an unreliable datagram service
            - the delays inherent in providing a reliable service may be unacceptable, especially in real-time applications such as multimedia

## 1.3.4 Service Primitives

### A service is formally specified by a set of primitives (operations) available to user processes to access the service
- tell the service to perform some action or report on an action taken by a peer entity
- system calls
    - cause a trap to kernel mode
    - send the necessary packets

> primitives for connection-oriented service
[](todo)

### steps

#### six packets are required to complete the protocol

- server executes LISTEN to indicate that it is prepared to accept incoming connections.
    - system call
    - server process blocked until a request for connection appears
- send packets
    - 1.client executes CONNECT to establish a connection with the server
        - sends a packet to the peer asking it to connect
        - parameter of server's address
        - client suspended until a response
    - 2.server can then establish the connection with the ACCEPT call
        - send a response to accept
        - arrival of response release the client
            - client && server now both running and have a connection established
- server to execute RECEIVE to prepare to accept the first request
    - block the server
    - when
        - normally immediately being released from the LISTEN
        - before the acknowledgement can get back to the client
- send packets
    - 3.client executes SEND to transmit its request
        - client: followed by execution of RECEIVE to get the reply
        - server: arrivial of request unblock the server to handle the request
    - 4.server: use SEND to return the answer
        - client: arrivial of the packet unblock the cient
    - additional requsts can go on(if have)
- send packets
    - 5.client: when done, executes DISCONNECT to terminate the connection
        -  an initial DISCONNECT is a blocking call, suspending the client 
        - sending a packet to the server saying that the connection is no longer needed
    - 6.server: gets the packet
        - issues a DISCONNECT of its own
        - acknowledging the client and releasing the connection
    - client receive the packet
        - client process is released
        - connection is broken

### issues
- timing can be wrong (e.g., the CONNECT is done before the LISTEN),
- packets can get lost
- why not connectionless:
    - in perfect world only two packets would be needed
        - request
        - reply
    - but
        - large messages in either direction (e.g., a megabyte file), 
        - transmission errors, 
        - lost packets

### problems of connectionless
- if packets lost, how would the client know if some pieces were missing?
- How would the client know whether the last packet actually received was really the last packet sent?
- How could it tell packet 1 from the second file from a lost packet 1 from the first file that suddenly found its way to the client?


## 1.3.5 services vs protocols
[image](todo)
- service 
    - like api
    - is a set of primitives that a layer provides to the layer above it
    - relates to an interface between two layers
        - lower layer -> service provider
        - upper layer -> service user
- protocol
    - like impl
    - relate to the packtets
    - is a set of rules relate to packets
        - governing the format and meaning of 
            - the packets, 
            - or messages that are exchanged by the peer entities within a layer.
- Many older protocols did not distinguish the service from the protocol
    - a typical layer might have had a service primitive SEND PACKET with the user providing a pointer to a fully assembled packet.
    - all changes to the protocol were immediately visible to the users ----- violate the rule/ a serious blunder

## 1.4 OSI Model && TCP/IP Model
- osi
    - protocols not used any more
    - model itself quite general && valid
    - features at each layer are still very important
- TCP/IP
    - the model itself is not of much use 
    - the protocols are widely used.

## 1.4.1 OSI
- developed by the International Standards Organization (ISO)
- ISO OSI (Open Systems Interconnection) Reference Model
    - means open for communication with other systems
- OSI model itself is not a network architecture 
    - it does not specify the exact services and protocols to be used in each layer
        - just tell what each layers should do
    - produced standards for all layers
        - these are not part of refernce model itself

> model(in part) widely used, protocols are long forgotten


## 1.4.1 OSI model layers

[image]()

### physical layer
- concerned with transmitting raw bits
- Typical questions 
    - what electrical signals should be used to represent a 1 and a 0, 
    - how many nanoseconds a bit lasts, 
    - whether transmission may proceed simultaneously in both directions, 
    - how the initial connection is established, 
    - how it is torn down when both sides are finished, 
    - how many pins the network connector has, and what each pin is used for.

### data link layer
-  transform a raw transmission facility into a line that appears free of undetected transmission errors
    - by masking the real errors so the network layer does not see them
    - break up the input data into data frames
        - transmit the frames sequentially
        - receiver confirms each frame by sending back an acknowledgement frame.
- issues
    - how to keep a fast transmitter from drowning a slow receiver in data.
    - some traffic regulation mechanism want to konw when the receiver can accept more data
    - Broadcast networks : how to control access to the shared channel.
        - A special sublayer added: medium access control sublayer

### the network layer
- controls the operations of the subnet
    - determining how packets are routed from source to destination.
    - handling congestion
    - the quality of service provided (delay, transit time, jitter, etc.
    - allow heterogeneous networks to be interconnected
        - different network may be different
            - in addressing
            - packet might be too large
            - protocol may differ

### the transport layer
- basic function
    - accept data from above it, 
    - split it up into smaller units if need be, 
    - pass these to the network layer, 
    - and ensure that the pieces all arrive correctly at the other end.
- determines what type of service to provide to the session layer, and, ultimately, to the users of the network--determined when connection is established
    -  most popular: error-free point-to-point channel
        - delivers messages or bytes in the order in which they were sent.
    - others
        - transporting of isolated messages with no guarantee about the order of delivery
        - the broadcasting of messages to multiple destinations.
- true end-to-end layer
    - carries data from source to destination
        - message headers
        - control message
    - lower layers not end-2-end
        - each protocols is between a machine and its immediate neighbors, 
        - not between the ultimate source and destination machines,
            - separated by many routers

### the session layer
- allows users on different machines to establish sessions between them.
    - dialog control 
        - (keeping track of whose turn it is to transmit), 
    - token management 
        - (preventing two parties from attempting the same critical operation simultaneously)
    - synchronization
        - (checkpointing long transmissions to allow them to pick up from where they left off in the event of a crash and subsequent recovery).

### presentation layer
- concerned with the syntax and semantics of the information transmitted.

### Application Layer
- ex. http

## 1.4.2 The TCP/IP Reference Model

[image]()

### history
-  ARPANET
    - using leased telephone lines
    - connect hundreds of universities and government
    - trouble with interworking with satellite and radio networks
    - a new reference architecture was needed --> tcp/ip
        - goals
            - connect multiple networks in a seamless way
            - be able to survive loss of subnet hardware(Soviet Union attack)
            - others: 
                - transferring files 
                - real-time speech transmission,

### The Link Layer
- describes what links such as serial lines and classic Ethernet must do to meet the needs of this connectionless internet layer.

### The Internet Layer
- linchpin that holds the whole architecture together
- deliver IP packets where they are supposed to go
    - IP (Internet Protocol): official packet format and protoco
    - ICMP (Internet Control Message Protocol)
- issues
    - Packet routing
    - congestion

### the transport layer
 - allow peer entities on the source and destination hosts to carry on a conversation
    - end-to-end transport protocols
        - TCP(Transmission Control Protocol)
            - reliable connection-oriented protocol
            - handles flow control to make sure a fast sender cannot swamp a slow receiver
        - UDP (User Datagram Protocol)
            - unreliable, connectionless protocol 
            - for applications that do not want TCP’s sequencing or flow control and wish to provide their own
            - widely used
                - one-shot, client-server-type request-reply queries
                - applications in which prompt delivery is more important than accurate delivery
                    - such as transmitting speech or video.

### The Application Layer
- virtual terminal (TELNET), 
- file transfer (FTP), 
- and electronic mail (SMTP)
- Domain Name System (DNS)
- HTTP, the protocol for fetching pages on the World Wide Web,
- RTP, the protocol for delivering real-time media such as voice or movies

## 1.4.4 OSI vs TCP/IP [compare in reference modules not the corresponding protocol stacks]
- layers:
    - osi: 4 layers
    - tcp/ip: 7 layers 
- both: layers up through and including the transport layer provide an end-to-end, network-independent transport service to processes wishing to communicate
- OSI devised before the corresponding protocols
- tcp/ip:protocols come first, 
    - model was really just a description of the existing protocols
    - no problems with protocols fitting the model, but model did not fit any other protocol stacks
        - it was not especially useful for describing other, non-TCP/IP networks.

### Three concepts are central to the OSI model:
- 1. Services.[methods , operations]
    - tells what the layer does, 
    - not how entities above it access it or how the layer works. 
    - It defines the layer’s semantics.
- 2. Interfaces.[methods' parameter && result]
    - tells the processes above it how to access it
    - specific parameters
    - not or how the layer works. 
- 3. Protocols.[code internal]
    - the layer’s own business.
    - can use any protocols to finish its work

### The TCP/IP model did not originally clearly distinguish between services, interfaces, and protocols
- ex. the only real services offered by the internet layer are SEND IP PACKET and RECEIVE IP PACKET
- the protocols in the OSI model compare that in the TCP/IP model
    - better hidden
    - replaced relatively easily as the technology changes

### connectionless vs connection-oriented
- osi
    - support both in network layer
    - only connection-oriented in transport layer
- tcp/ip
    - connectionless in network layer
    - both in transport layer
        - giving the user a choice--important for simple request-response protocols


## 1.4.5 critique of osi

### bad timing
- TCP/IP protocols were already in widespread use by research universities by the time the OSI protocols appeared
- When OSI came around, they did not want to support a second protocol stack until they were forced to

### bad technology
- seven layers was more political than technical, and two of the layers
    - (session and presentation) are nearly empty,
    - (data link and network) are overfull.
- extraordinarily complex
    - difficult to implement and inefficient in operation
- functions, such as addressing, flow control, and error control, reappear again and again in each layer.
    - unnecessary and inefficient.

### bad implementations
- implementations were huge, unwieldy, and slow.
- In contrast, one of the first implementations of TCP/IP was part of Berkeley UNIX and was quite good && free

### bad politics
- TCP/IP was thought as part of UNIX
- osi supported by the government but not work

## 1.4.6 critique of tcp/ip
- not clearly distinguish the concepts of services, interfaces, and protocols.
    - not much of a guide for designing new networks using new technologies
- the TCP/IP model is not at all general and is poorly suited to describing any protocol stack other than TCP/IP.
    - ex: completely impossible to use the TCP/IP model to describe Bluetooth
- the link layer is not a layer
    - it is an interface between the network and data link layer
- does not distinguish between the physical and data link layers.
    - physical layer: 
        - copper wire, fiber optics, and wireless communication
    - data link layer:
        - delimit the start and end of frames and get them from one side to the other with the desired degree of reliability.
- some protocols are ad hoc,  
    - ex. TELNET--The virtual terminal protocol
        - was designed for a ten-character-per-second mechanical Teletype terminal.
        - knows nothing of graphical user interfaces and mice
        - but still in used some 30 years later

> Ad hoc是拉丁文常用短语中的一个短语。这个短语的意思是“特设的、特定目的的（地）、即席的、临时的、将就的、专案的”。这个短语通常用来形容一些特殊的、不能用于其它方面的的，为一个特定的问题、任务而专门设定的解决方案


## 1.5.1 the internet

### not really a network at all
- a vast collection of different networks
    - use certain common protocols
    - provide certain common services

### The ARPANET
- 1950 DoD wants a command-and-control network that could survive a nuclear war.
    - telephone network
- 1960s, Baran proposed using digital packet-switching technology. AT&T dismissed
- 1957,  Soviet Union launch of the first artificial satellite, Sputnik--beat US
    - President Eisenhower--ARPA, the Advanced Research Projects Agency.
        - 1967, ARPA -- ARPANET (minicomputers + IMPs[Interface Message Processors])
            - first electronic storeand-forward packet-switching network
        - NPL system -- National Physical Laboratory in England
            - demonstrated that packet switching could be made to work
-  an experimental network went online
    - 1969 with four nodes: at UCLA, UCSB, SRI, and the University of Utah.
        - all had a large number of ARPA contracts
        - all had different and completely incompatible host computers (just to make it more fun).
    - ARPA also funded research on the use of satellite networks and mobile packet radio networks
    - demonstrated that the existing ARPANET protocols were not suitable for running over different networks
        -  1974--TCP/IP was specifically designed to handle communication over internetworks
- ARPA awarded several contracts to implement TCP/IP on different computer platforms,
    - IBM, DEC, and HP systems, as well as for Berkeley UNIX.
    - Berkeley UNIX--rewrote TCP/IP with a new programming interface called sockets for the upcoming 4.2BSD release of Berkeley UNIX
- 1980s, additional networks, especially LANs, connected to the ARPANET
    - DNS(Domain Name System) was created

### NSFNET
- 1970s,  NSF (the U.S. National Science Foundation) saw enormous impact of ARPANET
    - fund the Computer Science Network (CSNET) in 1981,
        - have to have a research contract with the DoD to get on ARPANET, many have no contract
        - connect to ARPANET via dial-up and leased lines
        - backbone network to connect supercomputer centers
            - Each supercomputer given a microcomputer called a fuzzball
                - fuzzball connected with 56kb lines to form subnet
                    - same hardware technology the ARPANET used
                - fuzzball use TCP/IP right from the start---first TCP/IP WAN
    - NSFNET: the complete network
        - funded some regional networks connected to the backbone
    - NSF encouraged MERIT, MCI, and IBM to form a nonprofit corporation, ANS (Advanced Networks and Services)
        - ANS took over NSFNET and upgraded to form ANSNET
        - 5 years later sold to America Online
    - NSF awarded contracts to four different network operators to establish a NAP (Network Access Point)
        - To ease the transition && make sure regional networks could communicate with each other
- 1990s,  many other countries and regions also built national research networks, 
    - often patterned on the ARPANET and NSFNET

## 1.5.1 Architecture of the Internet
- ISP: Internet Service Provider
- DSL: Digital Subscriber Line
    - DSL modem
        - modem is short for ‘modulator demodulator’ and refers to any device that converts between digital bits and analog signals.
    - DSLAM (Digital Subscriber Line Access Multiplexer)
- DSL + telephone line : dial-up
    -  limited to 56 kbps
- DSL + cable : much greater
- FTTH (Fiber to the Home)
    - optical fiber, 光纤
- POP (Point of Presence)
    - the ISP's POP:   locaiton at which customer packets enter the ISP network for service 
- TV system
    - cable modem
    - CMTS (Cable Modem Termination System)
- ISP’s POP (Point of Presence).
- backbone
    - long-distance transmission lines that interconnect routers at POPs in the different cities that the ISPs serve
- IXPs (Internet eXchange Points)
    - ISPs connect their networks to exchange traffic at IXPs
    - Basically, an IXP is a room full of routers,
        - at least one per ISP
        - A LAN in the room connects all the routers
    - a small ISP might pay a larger ISP for Internet connectivity
    - two large ISPs might decide to exchange traffic
- packet's path
    - depends on the peering choices of the ISPs
        - with destination ISP :directly to its peer
        - or else route the packet to the nearest paid transit provider
- tier 1 ISPs
    - form the backbone of the Internet
    - thousands of routers connected by high-bandwidth fiber optic links
    - AT&T, Sprint...
- data centers
    - Google, Yahoo...
    - server farm: rack upon rack of machines
    - Colocation/hosting (机器托管)
        - short, fast connections can be made between the servers and the ISP backbones

## 1.5.2 Mobile Phone Networks

### generation

#### AMPS : Advanced Mobile Phone System , 1G
    - transmitted voice calls as continuously varying(Analog) signals
    - each voice call on a specific frequency band

#### GSM : Global System for Mobile communications
    - transmitting voice calls in digital form
#### UMTS (Universal Mobile Telecommunications System) || WCDMA (Wideband Code Division Multiple Access)
    - offer both digital voice and broadband digital data services
    - each cell to use all frequencies,
    - a tolerable level of interference to the neighboring cells.
    - based on Code Division Multiple Access (CDMA)

#### radio access network
    - cellular base station
    - controller node or RNC (Radio Network Controller)
        - controls how the spectrum is used.

#### core network: carries the traffic for the radio access network
- UMTS core network evolved from 2G GSM system's core network
    - connectionless subnets--packet networks
        - if too many packets, router will choke and lose packets
            - sender will resend but service will be jerky
    - connection-oriented subnets--circuit networks
        - set up establishes a route
            - all words or packets follow the same route
            - if a line or switch on the path goes down, call is aborted
        - support quality of service more easily
            - subnet can reserve resources(link bandwidth, switch buffer space, CPU)
            - if resource is insufficient, call will be rejected
            - once a connection is connected, connection would get good service
- old mobile phone networks--connectionless
    - MSC (Mobile Switching Center), GMSC (Gateway Mobile Switching Center)
    - MGW (Media Gateway) elements that set up connections over a circuit-switched core network such as the PSTN (Public Switched Telephone Network).
- GPRS(General Packet Radio Service)
    -  ran at tens of kbps
-  UMTS core network nodes connect directly to a packet-switched network
    - SGSN (Serving GPRS Support Node) and the GGSN (Gateway GPRS Support Node)
    - voice calls over a packet data network

#### issues
- handover
    - soft handover
        - connect to the new base station before disconnecting from the old base station
    - hard handover

- how to find a mobile in the first place when there is an incoming call
    - Each mobile phone network has a HSS (Home Subscriber Server)
        - the location of each subscriber,
        - profile information that is used for authentication and authorization

- security
    - SIM card, short for Subscriber Identity Module
        - authenticate subscribers
        - cryptographic keys on the SIM card are used to encrypt transmissions

## 1.5.3 wifi, 802.11
- operate in unlicensed bands such as the ISM (Industrial, Scientific, and Medical) bands 
    - defined by ITU-R (e.g., 902-928 MHz, 2.4-2.5 GHz, 5.725-5.825 GHz).
- two style
    - Wired network with APs(access points)
    - ad hoc network: clients talk directly
- multipath fading
    - radio signals reflected off solid objects
        - multiple echoes of a transmission
            - cancel
            - reinforce
    - solutions:
        - path diersity: sending along multiple, independent paths
        - using different frequencies
        - repeating bits over different periods of time
- multiple transmissions collide
    - CSMA (Carrier Sense Multiple Access)
        - Computers wait for a short random interval before transmitting, and defer their transmissions if they hear that someone else is already transmitting
- security
    - WEP (Wired Equivalent Privacy)
    - WiFi Protected Access, initially called WPA but now replaced by WPA2


## 1.5.4 RFID and Sensor Networks

### Radio Frequency IDentification (RFID)
- consists of a small microchip with a unique identifier and an antenna that receives radio transmissions.

#### battery
- passive RFID
    - all of the energy needed to operate them is supplied in the form of radio waves by RFID readers.
- active RFID
    - there is a power source on the tag

#### distance
- UHF RFID (Ultra-High Frequency RFID).
    - shipping pallets and some drivers licenses
    - backscatter：Tags communicate at distances of several meters by changing the way they reflect the reader signals
- HF RFID (High Frequency RFID)
    - passport, credit cards, books, and noncontact payment systems
    - based on induction
- LFRFID (Low Frequency RFID)
    - for animal tracking

#### problem
- multiple tags
    - tags wait for a short random interval before responding with their identification
- security
    - weak measures: like passwords

### sensor network
- Sensor nodes are small computers
- self-organize to relay messages for each other


## 1.7 METRIC UNITS

https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fslideplayer.com%2F5675735%2F18%2Fimages%2F4%2FMetric%2BConversion%2BChart.jpg&refer=http%3A%2F%2Fslideplayer.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1622127822&t=f468c0add4a2c6f3b629c2b95844ce87


## 3.1 data link layer


uses the services of the physical layer to send and receive bits over communication channels
- Providing a well-defined service interface to the network layer.
- Dealing with transmission errors.
- Regulating the flow of data

## 3.1.1 services provided to the natwork layer

### Unacknowledged connectionless service.
having the source machine send independent frames to the destination machine without having the destination machine acknowledge them
- ex. Ethernet,
- no logical connection
- if a frame is lost, no attempt to detect the loss or recover
- appropriate when the error rate is very low,  appropriate for real-time traffic, such as voice

### Acknowledged connectionless service.
- no logical connections
- each frame sent is individually acknowledged
- is useful over unreliable channels, such as wireless systems. ex.  802.11 (WiFi)

### Acknowledged connection-oriented service

#### providing acknowledgements in the data link layer is just an optimization, never a requirement

- can send packets and wait for ack, resend when expired
    - inefficient, 
- network layer does not know hardware parameters, and send a big packet(ex.10 frames, 2 lost):
    - links have a strict maximum frame length imposed by hardware
    - known propagation delays.
- On reliable channels, such as fiber, the overhead of a heavyweight data link protocol may be unnecessary, 
- but on (inherently unreliable) wireless channels it is well worth the cost

#### connection-oriented
- Each frame sent over the connection is numbered, 
- guarantees that each frame sent is indeed received.
- guarantees that each frame is received exactly once and that all frames are received in the right order.
- appropriate over long, unreliable links such as a satellite channel or a long-distance telephone circuit.
    - if connectionless, cause a frame to be sent and received several times, wasting bandwidth.

## 3.1.2 framing
- channel is noisy
- Breaking up the bit stream into frames
    - Byte count.
        - 无法解决乱序问题
    - Flag bytes with byte stuffing.
        - two consecutive flag bytes indicate the end of one frame and the start of the next
        - insert special escape byte(ESC) to avoid interfering
        - flaw: tied to the use of 8-bit bytes
    - Flag bits with bit stuffing.
        - framing done at the bit level
        - Each frame begins and ends with a special bit pattern, 01111110 or 0x7E in hexadecimal.
            - to avoid conflict in the body
                - sender: stuffs 0, when encounters five consecutive 1s
                - receiver: automatically destuffs the 0 bits when sees 
    - Physical layer coding violations.

![bit stuffing](/Users/thomasliao/Documents/github/liao123abc.github.io/images/bit_stuffing_mechanism.jpeg)

>  A common pattern used for Ethernet and 802.11 is to have a frame begin with a well-defined pattern called a preamble.

## 3.1.3 Error Control

> ensure that each frame is ultimately passed to the network layer at the destination exactly once, no more and no less

- provide the sender with some feedback
    - positive ack: arrived
    - negative ack: something go wrong
- if a frame vanish?
    - introducing timers
        - expire after an interval
- if either the frame or the ack is lost?
    - just transmit the frame again
- if sender transmit the same frame multiple times, receiver accept the same frame two or more time
    - assign sequence numbers to outgoing frames

## 3.1.4 flow control
> a sender that systematically wants to transmit frames faster than the receiver can accept them.

### issues
- sender: a fast, powerful computer and the receiver: a slow, low-end machine.
    - phone request a web page from powerful server

### commonly used approaches
- feedback-based flow control
    - receivers send back information to the sender to send more data
    - or at least telling the sender how the receiver is doing
- rate-based flow control
    - the protocol has a built-in mechanism that limits the rate at which senders may transmit data, 
    - without using feedback from the receiver.

### feedback vs rate
- Feedback-based schemes are seen at both the link layer and higher layers
    - more common:
        - the link layer hardware is designed to run fast enough that it does not cause loss.
        - hardware implementations of the link layer as NICs (Network Interface Cards) can handle frames as fast as they can arrive on the link
            - so handled by higher layers
    - various feedback-based flow control schemes: the same basic principle
        - well-defined rules about when a sender may transmit the next frame. 
        - These rules often prohibit frames from being sent until the receiver has granted permission,
- rate-based schemes are only seen as part of the transport layer


## 3.2 ERROR DETECTION AND CORRECTION

### strategies dealing with errors
- error-correcting codes || FEC (Forward Error Correction).
    - include enough redundant information to enable the receiver to deduce what the transmitted data must have been.
    - used On channels that are highly reliable, such as fiber
        - it is cheaper to use an error-detecting code and just retransmit the occasional block found to be faulty
    - seen in the physical layer, particularly for noisy channels
    - in higher layers, particularly for real-time media and content distribution
- error-detecting codes
    - include only enough redundancy to allow the receiver to deduce that an error has occurred (but not which error)
        - have it request a retransmission.
    - FEC is used on noisy channels because retransmissions are just as likely to be in error as the first transmission
        - wireless links: make many errors
    - commonly used in link, network, and transport layers

> Neither error-correcting codes nor error-detecting codes can handle all possible errors

## 3.2.1 Error-correcting codes--todo
1. Hamming codes.
2. Binary convolutional codes.
3. Reed-Solomon codes.
4. Low-Density Parity Check codes.

## 3.2.2 Error-Detecting Codes--todo
1. Parity.
2. Checksums.
3. Cyclic Redundancy Checks (CRCs).

## 3.4 sliding window

> The network layer, in contrast, is always fed data in the proper order, regardless of the data link layer’s window size

- each outbound frame contains a sequence number,ranging from 0 up to some maximum.
- sender maintains a set of sequence numbers corresponding to frames it is permitted to send.
    - These frames are said to fall within the sending window
- receiver also maintains a receiving window corresponding to the set of frames it is permitted to accept.

### sequence number in sender
- The sequence numbers within the sender’s window represent 
    - frames that have been sent 
    - or can be sent but are as yet not acknowledged.
- maintains a list of unacknowledged frames
    - when a new packet arrives
        - it is given the next highest sequence number,
        - the upper edge of the window is advanced by one

### sequence number in receiver
- frame receive, 
    - if (frame.sq == window.lowerEdge)
        - it is passed to network layer
        - window is rotated by one
    - any frame falling outside the window is discarded
- ack
    - a window size of 1 means that the data link layer only accepts frames in order, 
        - but for larger windows this is not so


## 3.4.1 Sliding Window Protocol

### go-back-n
is for the receiver simply to discard all subsequent frames, sending no acknowledgements for the discarded frames.

### selective repeat
a bad frame that is received is discarded, but any good frames received after it are accepted and buffered.

### flow control

### error control

## 4 the medium access control sublayer


## 5.1.2 Services Provided to the Transport Layer
> provides services to the transport layer

### rules:
- The services should be independent of the router technology
- transport layer should be shielded from the number, type, and topology of the routers present
- network addresses should use a uniform numbering plan, even across LANs and WANs

### two camps
- connectionoriented service 
    - routers’job is moving packets around and nothing else
    - host: error control(error detection and correction) && flow control
- connectionless service
    - represented by telephone companies
    - ATM vs IP
    - connection-oriented technologies
        - MPLS (MultiProtocol Label Switching)
        - VLANs
    
## 5.2.1 the optimality principle



## 6.1.1 Services Provided to the Upper Layers

### different kinds similar with network service
- connection-oriented 
- connectionless,

### why need transport layer

#### make the transport service to be more reliable
- The transport code runs entirely on the users’ machines, but the network layer mostly runs on the routers
    - routers operated by the carrier
    - users have no real control over network layer
        - cannot solve the problem of poor service by using better routers
        - cannot putting more error handling in the data link layer
    - solution: put on top of the network layer another layer--transport layer
        - packets are lost or mangled in connectionless
            - transport layer detect the problem and compensate for it by retransmit
        - in connnection-oriented network, connection is abrutly terminated
            - set up new network connection

#### hiding the network service behind a set of transport service primitives
- transport primitives can be implemented as call to library procedures
    - independent of the network primimitives
- application programmers can write code according to a standard set of primitives
    - without having to worry about different network interfaces and reliability

#### transport layer--key position
- the bottom four layers--transport service provider
- the upper layers --transport service user


## 6.1.2

## 6.1.3 Berkeley Sockets

### what
- A common transport layer interface
- another set of transport primitives, 
- Sockets the de facto standard for abstracting transport services to applications.
    - often used with the TCP protocol to provide a connection-oriented service called a reliable byte stream
        - other protocols work as well
    - used with a connectionless transport service

### parameters of the call specify 
- the addressing format to be used, 
- the type of service desired (e.g., reliable byte stream), 
- and the protocol

### The socket primitives for TCP
- SOCKET Create a new communication endpoint
- BIND Associate a local address with a socket
- LISTEN Announce willingness to accept connections; give queue size
- ACCEPT Passively establish an incoming connection
- CONNECT Actively attempt to establish a connection
- SEND Send some data over the connection
- RECEIVE Receive some data from the connection
- CLOSE Release the connection





## 6.2 transport protocol vs data link protocol


### major difference: operating environment

> data link layer -- communicate directly via a physical channel(wired || wireless)

> transport layer -- communicate via the entire network

#### addressing
- data link layer:
    - not necessary for a router to specify which router it wants to talk to 
- transport layer:
    - addressing of destinations is required

#### establishing a connection
- data link layer : simple
    - the other end is always there
- transport layer: complicated

#### the potential existence of storage capacity in the network
- data link layer:
    - a packet may arrive or be lost
- transport layer:
    - delay and duplicate packets
        - take scenic route
        - arrive late 
        - out of expected order

#### degree: buffering and flow control
- data link layer:
    - a large and varying number of connections with bandwith that fluctuates
    - not buffers to each line
- transport layer:
    - buffers to each line


## 6.2.2 connection establishment

> network can lose, delay, corrupt, duplicate packets

### issues e.g.

- delayed in the network and pop out much later, when the sender thought that they had been lost

###  prevent packets from being duplicated and delayed
- ...
    - throwaway transport addresses
        - more difficult to connect with a process in the first place
    - give each connection a unique identifier
        - requires each transport entity to maitain a certain amount of history infomation indefinitely
- kill off aged packets that hobbling about
    - restricted network design -- prevents packets from looping
    - hop counter
    - timestamping