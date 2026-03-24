Q: What are the seven layers of the OSI model?
A: Physical, Data Link, Network, Transport, Session, Presentation, Application (bottom to top).

---

Q: What is the difference between TCP and UDP?
A: TCP is connection-oriented, reliable, and ordered — it guarantees delivery via handshake and retransmission. UDP is connectionless and best-effort — faster but with no delivery guarantee.

---

Q: What is the TCP three-way handshake?
A: The process to establish a TCP connection: client sends SYN, server replies SYN-ACK, client replies ACK. After this the connection is established and data can flow.

---

Q: What is a subnet mask and what does it define?
A: A 32-bit value that divides an IP address into network and host portions. It defines how many addresses belong to the same network (e.g., /24 = 256 addresses, 254 usable hosts).

---

Q: What is CIDR notation?
A: Classless Inter-Domain Routing — a compact way to express an IP address and its subnet mask (e.g., 192.168.1.0/24), where the number after the slash is the count of network bits.

---

Q: What is the purpose of DNS?
A: Domain Name System — translates human-readable hostnames (e.g., example.com) into IP addresses. It is a distributed, hierarchical system of resolvers and authoritative name servers.

---

Q: What is the DNS resolution sequence?
A: Browser cache → OS cache → recursive resolver → root name server → TLD name server → authoritative name server → returns IP to client.

---

Q: What is the difference between a switch and a router?
A: A switch operates at Layer 2 (Data Link) and forwards frames within the same network using MAC addresses. A router operates at Layer 3 (Network) and forwards packets between different networks using IP addresses.

---

Q: What is ARP (Address Resolution Protocol)?
A: A Layer 2 protocol that resolves an IP address to a MAC address within a local network — a host broadcasts "who has IP X?" and the owner replies with its MAC address.

---

Q: What is NAT (Network Address Translation)?
A: A technique where a router rewrites source/destination IP addresses in packets — commonly used to allow many private IPs to share a single public IP for internet access.

---

Q: What is BGP and what is it used for?
A: Border Gateway Protocol — the routing protocol of the internet. It exchanges reachability information between autonomous systems (ASes), determining the best paths for inter-domain traffic.

---

Q: What is the difference between HTTP and HTTPS?
A: HTTP transfers data in plaintext over TCP port 80. HTTPS wraps HTTP in a TLS session (port 443), providing encryption, authentication, and integrity.

---

Q: What is a MAC address?
A: A 48-bit hardware identifier burned into a network interface card, used at Layer 2 to uniquely identify a device within a local network segment.

---

Q: What is the purpose of ICMP?
A: Internet Control Message Protocol — used for network diagnostics and error reporting. `ping` uses ICMP Echo Request/Reply; `traceroute` uses ICMP Time Exceeded messages.

---

Q: What is a VLAN?
A: Virtual Local Area Network — a logical network segment created on a switch that isolates traffic between groups of ports as if they were on separate physical switches, without additional hardware.
