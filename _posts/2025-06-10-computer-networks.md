---
layout: post
title: "Inside the Internet’s Nervous System: OSI and layer protocols"
subtitle: "A brief overview of the 7-layer model and some of the protocols that keep our data flowing"
---

Imagine you're watching Netflix, or joining a group video call with teammates across the globe, or simply messageing a friend. For all of these, your device has to be engaged in a very real, very structured conversation with other machines in the world. This invisible behind the scenes dialogue is the work of computer networks.

Networking is a core part of any system you design -- you'll always have independent machines exchanging data over a network. Fundamentally, a network is just a medium that lets these machines exchange information. But how do these machines agree on how to talk? How do messages go to the right place? What happens if something goes wrong along the way?

In this post, we'll briefly demystify how computers talk. Note that the field of computer networks is extremely vast and this blog (along with others I write on this topic) will just cover those pieces of computer networks you absolutely should know as a software developer.

### Networking layers

When we talk about how data moves across the internet, from your laptop to a server across the globe, it helps to think in layers. The OSI Model (Open Systems Interconnection Model) gives us a layered view of this journey. It is a conceptual framework that breaks the communication process into seven layers, each having a specific role in network communication. When your computer sends data, whether it’s a chat message, a video stream, or a web page, it doesn't just shoot that data across the internet in one go. Instead, it follows a carefully structured journey through the layers of the OSI model.

![OSI Model](/img/sys_design/osi.png)

Each layer in the OSI model relies on the services of the layer below it while providing services to the layer above it. On sender's side, the message goes down the OSI layers, gaining extra instructions at each step (encapsulation) so that it can be sent over the network successfully. On reciever's side, the message goes up the OSI layers, with each layer unwrapping the metadata added by the corresponding layer on the sender's side (decapsulation). This is called "layer-to-layer communication", and it's like having a secret language between each pair of layers across machines.

How does the OSI model help?
1. Standardization: OSI model standardizes the communication process ensuring different devices can communicate with each other.
2. Learning tool: The layered abstraction allows us to learn about the network communication one layer at a time. Thus, the OSI model encourages a compartmentalized way of thinking, making the complex world of network communication more digestible.

While the OSI model has seven layers, most developers arguably only need to focus on three key ones: Layer 3 (Network), Layer 4 (Transport), and Layer 7 (Application). But let’s zoom out briefly to see what each layer does:
1. Application layer Block (Layers 5-7 combined): Although the OSI model defines three separate layers at the top (Application, Presentation, and Session), in real-world use, we usually treat them as a single block: the Application Layer. This is where the magic starts -- where users interact with software like web browsers, Zoom, or email apps. Think of it as the front door to network communication -- everything else supports what happens here.
2. Transport Layer (Layer 4): This layer is responsible for ensuring reliable/fast data transmission and splitting data into chunks (called segments). Analogy: Imagine trying to send a book through the mail, but all you have are small envelopes. So, you tear out each page, place it in an envelope, and number them. The Transport Layer does exactly this -- it splits your data into parts, labels them, and keeps track of what was sent and received.
3. Network Layer (Layer 3): Here’s where the addressing and navigation happens. This layer decides where data goes and plots the best route to get there (routing). It uses IP addresses, much like street addresses, to make sure your data reaches the right destination—whether that’s across the room or across the ocean. Analogy: You’ve addressed your envelopes with the recipient’s house number and ZIP code and the postal system has figure out the fastest, most efficient delivery route.
4. Data Link Layer (Layer 2): This layer takes care of communication within a local neighborhood i.e. between devices on the same network. It wraps your data into frames, tags it with a MAC address (like a hardware name tag), and uses error detection mechanisms like checksums to identify corrupted transmissions. It also performs media access control i.e. it ensures that devices don’t all “talk” at once, which would cause network noise, thus acting as a moderator to determine how devices access and share the physical communication medium, helping prevent collisions and maintain orderly data flow. Analogy: Think of a traffic officer at a busy intersection, letting cars (data frames) move in turn, directing them to the right driveways (MAC addresses), and checking their license plates (checksums) for legitimacy.
5. Physical Layer (Layer 1): Finally, we get to the bottom of the stack, the Physical Layer. This is the layer that handles the actual transmission of raw bits, 1s and 0s, over physical channels like Ethernet cables, fiber optics, or even Wi-Fi radio waves. This layer converts digital data from the Data Link Layer into electrical, optical, or electromagnetic signals, and vice versa on the receiving end.  It defines how fast the bits are sent, what voltage levels mean “1” or “0”, and how those bits are physically encoded. It doesn’t know or care what the data means, it just makes sure it gets from one end to the other.

### Layer Protcols
Like we discussed above, each layer in the OSI framework only talks to its peer layer on the other side. For this, each layer follows a certain protcol -- think of layer protocols like a language contract which enable peer layers on different machines interpret the messages being sent and received. For example:
- The Transport Layer might use TCP or UDP to manage reliable or fast delivery.
- The Network Layer uses IP to handle addressing and routing.
- The Application Layer could use HTTP, SMTP, or DNS, depending on the task.

#### Network Layer Protocols

![IP](/img/sys_design/ip.png)

IP (Internet Protocol) dominates the Network layer of the OSI model to handle addressing and routing -- figuring out where your data is going and how to get it there. IP breaks down the data segments coming from the Transport Layer (Layer 4) into smaller chunks called packets, adds an IP header (20-60 Bytes) to each packet to labels it with the source and destination IP addresses, and then figures out how to get each packet across multiple networks to its destination. IP doesn't care what's inside the packet, it just moves it from one place to another using routers. Also, IP doesn’t guarantee delivery, order, or error checking. It just pushes the packet out the door and hopes it gets there (that's where Transport Layer Protocols come in). 

What's an IP Address? An IP address is like a home address for any machine on the internet or a local network -- it helps other machines know where to send information. When a machine connects to a network (either physically through cables or wirelessly through Wi-Fi), the machine sends a DHCP (Dynamic Host Configuration Protocol) request to ask the local router (which is the only device it can talk to at the moment) for an IP address. The DHCP server (on the router) keeps a list of available IP addresses and assigns one to the machine. The machine can send and receive information over the network now.

| Type           | Visible on the Internet? | Used For                             | Assigned By             |
| -------------- | ------------------------ | ------------------------------------ | ----------------------- |
| **Public IP**  | Yes                      | Talking to the wider internet        | Internet provider (ISP) |
| **Private IP** | No                       | Inside local networks (home, office) | Your router (via DHCP)  |


There are 2 versions of IP addresses:
1. IPv4: The older and still most widely used version. Example address: `192.168.0.1` -- it has 32 bits in total, and each number in the address (like `192`) is 8 bits, and there are 4 groups of them. This allows for about 4.3 billion unique IPv4 addresses, which can't handle the exploding number of devices on the internet.
2. IPv6: The newer version designed to support the massive scale of the modern internet. It has 128 total bits -- 128 bits are split into 8 groups, and each group is 16 bits. Exmaple address: `2001:0db8:85a3:0000:0000:8a2e:0370:7334` 

#### Transport Layer Protocols
This layer is dominated by 3 protocols: TCP, UDP, and QUIC. Depending on what guarantees you want, you can choose either of them to communicate with other machines.

##### UDP (User Datagram Protocol) --  Quick and Dirty Delivery Service
UDP is one of the core protocols used to send data across any network. It's fast and efficient but that comes at the cost of UDP being unreliable. Data chunks coming from the Application Layer (Layer 5-7) are packaged into a UDP datagrams by adding a UDP header (8 Bytes):
- Source Port: Port of the sender (16 bits)
- Destination Port: Port of the receiver (16 bits)
- Length: Length of the UDP datagram (16 bits)
- Checksum: Optional error checking for the datagram (16 bits)

![UDP Datagram](/img/sys_design/udp_packet.png)

These UDP datagrams are then sent directly without setting up a connection with the receiver -- there's no guarantee of delivery. On its own, UDP doesn’t confirm if the data arrived, it doesn't do retransmission of lost or corrupted datagrams, and datagrams might arrive out of order on the reciever's side -- Application developers must implement their own handling for retransmission or ordering if needed. Thus, UDP is a connectionless, stateless, and lightweight protocol -- think of it like sending a postcard instead of a registered letter. 

You should use UDP when:
- Speed and low latency are more critical than perfect delivery.
- You can tolerate some data loss.
- You’re working with real-time or broadcast applications.

Common use cases that rely on UDP under the hood or in niche ways:
1. Streaming Media (Audio/Video): Real-time playback is more important than re-transmitting lost packets (e.g.,: Netflix, YouTube)
2. VoIP (Voice over IP) and Video Conferencing: Speed is more important than perfection. It’s OK if a word or pixel drops here or there, as long as the conversation flows smoothly (e.g., Zoom, WhatsApp)
3. DNS (Domain Name System): DNS queries are small and fast; retransmission can be handled by the application if needed
4. Online Gaming: Fast reactions are key. It's more important to get updates quickly than perfectly
5. Sensor Networks and IoT devices: These devices don’t need perfect delivery but quick and light communication to save battery and power (e.g., A weather sensor in your garden)
6. Log Shipping and Telemetry: Logs/metrics are typically fire-and-forget and tolerant to loss
7. Video Surveillance Systems: Dropped frames are acceptable but delay is not
8. One-to-many communication: Broadcasting and multicasting are easier with UDP because UDP is connectionless and doesn't require a one-to-one relationship like TCP does (e.g., Finding Devices on a Network)

If you’re sending important data (like emails or files), you’d want something more reliable, like TCP, which checks for errors and delivery.

##### TCP (Transmission Control Protocol) --  Reliable but Fussy Courier Service
TCP is like sending a package with a tracking number and delivery confirmation. TCP is a connection based protocol i.e. it sets up  a reliable connection between sender and receiver before sending any data. To establish a connection, TCP uses a 3 way handshake -- a machine sends a SYN (Start of connection) request to the other machine, the other machine responds with SYN-ACK (Start of connection + Acknowledgement), the first machine responds back with ACK (Acknowledgement). Think of it like when you call someone, you wait for them to pick up, then you both say "Hello" before any real communication begins. 

Data chunks coming from the Application Layer (Layer 5-7) are packaged into a TCP segments by adding a TCP header (20-60 Bytes), which includes:
- Source and Destination Ports
- Sequence numbers: The part of the conversation this data belongs to
- Acknowledgment numbers: Confirms safe delivery of everything received so far and tells the sender exactly where to resume
- Checksum
- TCP flags: Whether it’s starting, continuing, or ending a conversation

![TCP Segment](/img/sys_design/tcp_packet.png)

The header also includes other essential information to ensure reliable, ordered communication. As you can see, the TCP header is quite bulky compared to the UDP header. Every TCP segment that is sent is numbered (so it arrives in order), checked for damages (error checking), and confirmed when delivered (acknowledgement). If something is lost or corrupted, it is automatically re-sent. TCP also implements:
- Flow control: Prevent the sender from sending data faster than the receiver can process it. It's implemented using a sliding window mechanism and a field in the header called the Window Size (how many bytes the receiver can take).
- Congestion control: Even if the receiver can handle more, the network in between might be struggling. TCP watches for signs of congestion (like packet loss or delays) and slows down when needed.

![TCP Flow](/img/sys_design/tcp_connection.png)

Ending a TCP connection is like saying a polite goodbye on a phone call. TCP uses four steps and special messages with the FIN and ACK flags: The side that wants to close the connection (say, Client) sends a FIN (Finish) flag (indicating that it has no more data to send to the server and wants to close the connection) -> The other side (say, Server) sends back an ACK to confirm it got the FIN (At this point, the server can still send any data to the client) -> When the server finishes sending its data, it also sends a FIN -> Client replies with ACK, confirming the server’s FIN. TCP wants to make sure all data is safely delivered before closing the connection. That’s why it doesn’t just cut the cord — it confirms both sides are truly finished. Note that TCP connection is persistent and one of the two machines involved in the communication has to initiate the TCP connection teardown for the communication to end.

Everyday use cases that rely on TCP:
1. Web browsing (HTTP/HTTPS): Reliable, ordered delivery of web pages and resources
2. Emails (SMTP, IMAP): TCP ensures complete and correct delivery of messages
3. File transfers (FTP, SFTP): Accuracy and reliability in transferring large files
4. Remote access (SSH): TCP ensures secure and uninterrupted command-line sessions
5. Software Updates and Package Managers: Tools like `apt`, `yum`, `pip` use TCP as it ensures files are downloaded completely and correctly
6. Even cybercriminals use TCP because it’s reliable and can be disguised as normal traffic (like HTTP)

##### QUIC --  Fast & Secure Express
If TCP is a reliable delivery truck, QUIC (Quick UDP Internet Connections) is a smart, encrypted drone that avoids traffic, flies directly to your window, and drops off multiple packages at once. QUIC, developed by Google, fixes 2 big problems with the traditional transport layer protocols:
1. Slow start: TCP and TLS (Transport Layer Security, encrypts and secures the data) handshakes require multiple round trips between the client and server. QUIC solves this by combining transport and encryption in a single handshake — saving time and improving performance.
2. Head-of-line Blocking: TCP guarantees in-order delivery to the recipient. If a packet (say, packet #1) is lost, TCP waits for it to be resent before sending the later packets (like #2, #3…) to the recipient. QUIC (used in HTTP/3) solves this by allowing multiple independent streams -- like separate lanes on a highway. Lost packets only affect the stream they belonged to — not all streams.

QUIC makes web browsing faster and more secure by rethinking how data is delivered over the internet.

### Sockets
Practically, the OS Kernel handles all the networking logic:
- Receiving and assembling IP packets
- Managing ports, connections, reliability etc

A socket is a software endpoint for network communication. It is like a doorway and the OS behind it does the heavy lifting i.e. sockets are an interface between the end user applications and the OS network stack. Applications send and receive data via sockets. The abstraction hides the complexities of the network protcols (like TCP, UDP, and IP) and provides a standard programming interface. A socket is identified by an IP address and a port number. When combined with the protcol (TCP or UDP), it defines a communication endpoint.

Nothing will teach you how TCP and UDP works more than implementing them yourselves -- see the examples here for how to implement a very basic TCP/UDP server handling multiple clients concurrently through a threadpool.

We'll cover Application Layer Protocols like HTTP in the next post. 
