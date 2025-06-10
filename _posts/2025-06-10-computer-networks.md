---
layout: post
title: "From Bits to Browsers: How the OSI Model Shapes the Web"
subtitle: "A brief overview of the 7-layer model and the network protocols that keep our data flowing"
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
IP (Internet Protocol) dominates the Network layer of the OSI model to handle addressing and routing -- figuring out where your data is going and how to get it there. IP breaks down the data segments coming from the Transport Layer (Layer 4) into smaller chunks called packets, adds an IP header to each packet to labels it with the source and destination IP addresses, and then figures out how to get each packet across multiple networks to its destination. IP doesn't care what's inside the packet, it just moves it from one place to another using routers. Also, IP doesn’t guarantee delivery, order, or error checking. It just pushes the packet out the door and hopes it gets there (that's where Transport Layer Protocols come in). 

What's an IP Address? An IP address is like a home address for any machine on the internet or a local network -- it helps other machines know where to send information. When a machine connects to a network (either physically through cables or wirelessly through Wi-Fi), the machine sends a DHCP (Dynamic Host Configuration Protocol) request to ask the local router (which is the only device it can talk to at the moment) for an IP address. The DHCP server (on the router) keeps a list of available IP addresses and assigns one to the machine. The machine can send and receive information over the network now.

| Type           | Visible on the Internet? | Used For                             | Assigned By             |
| -------------- | ------------------------ | ------------------------------------ | ----------------------- |
| **Public IP**  | Yes                      | Talking to the wider internet        | Internet provider (ISP) |
| **Private IP** | No                       | Inside local networks (home, office) | Your router (via DHCP)  |


There are 2 versions of IP addresses:
1. IPv4: The older and still most widely used version. Example address: `192.168.0.1` -- it has 32 bits in total, and each number in the address (like `192`) is 8 bits, and there are 4 groups of them. This allows for about 4.3 billion unique IPv4 addresses, which can't handle the exploding number of devices on the internet.
2. IPv6: The newer version designed to support the massive scale of the modern internet. It has 128 total bits -- 128 bits are split into 8 groups, and each group is 16 bits. Exmaple address: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

To add: CIDR routing 


#### Transport Layer Protocols
This layer is dominated by 3 protocols: TCP, UDP, and QUIC. Depending on what guarantees you want, you can choose either of them to communicate with other machines.

##### UDP (User Datagram Protocol) --  Quick and Dirty Delivery Service
UDP is one of the core protocols used to send data across any network. It's fast and efficient but that comes at the cost of UDP being unreliable. Data chunks coming from the Application Layer (Layer 5-7) are packaged into a UDP datagrams by adding a UDP header (8 Bytes):
- Source Port: Port of the sender (16 bits)
- Destination Port: Port of the receiver (16 bits)
- Length: Length of the UDP datagram (16 bits)
- Checksum: Optional error checking for the datagram (16 bits)

These UDP datagrams are then sent directly without setting up a connection with the receiver -- there's no guarantee of delivery. On its own, UDP doesn’t confirm if the data arrived, it doesn't do retransmission of lost or corrupted datagrams, and datagrams might arrive out of order on the reciever's side -- Application developers must implement their own handling for retransmission or ordering if needed. Thus, UDP is a connectionless, stateless, and lightweight protocol -- think of it like sending a postcard instead of a registered letter. 

You should use UDP when:
- Speed and low latency are more critical than perfect delivery.
- You can tolerate some data loss.
- You’re working with real-time or broadcast applications.

Common use cases for UDP:
1. Streaming Media (Audio/Video): Real-time playback is more important than re-transmitting lost packets (eg: Netflix, YouTube)
2. VoIP (Voice over IP) and Video Conferencing: Speed is more important than perfection. It’s OK if a word or pixel drops here or there, as long as the conversation flows smoothly (e.g., Zoom, WhatsApp)
3. DNS (Domain Name System): DNS queries are small and fast; retransmission can be handled by the application if needed
4. Online Gaming: Fast reactions are key. It's more important to get updates quickly than perfectly

If you’re sending important data (like emails or files), you’d want something more reliable, like TCP, which checks for errors and delivery.
