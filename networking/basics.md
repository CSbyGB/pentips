# Networking basics

## Internal Networks

"Internal Networks are subnetworks that are segmented and separated based on the importance of the internal device or the importance of the accessibility of its data. The main purpose of the internal network(s) is to share information, faster and easier communications, collaboration tools, operational systems, and network services within an organization. In a corporate network, the network administrators intend to use network segmentation for various reasons, including controlling network traffic, optimizing network performance, and improving security posture."  

![internal network](../.res/2022-09-20-09-01-22.png)

Source: tryhackme

## A Demilitarized Zone (DMZ)

"A DMZ Network is an edge network that protects and adds an extra security layer to a corporation's internal local-area network from untrusted traffic. A common design for DMZ is a subnetwork that sits between the public internet and internal networks.

Designing a network within the company depends on its requirements and need. For example, suppose a company provides public services such as a website, DNS, FTP, Proxy, VPN, etc. In that case, they may design a DMZ network to isolate and enable access control on the public network traffic, untrusted traffic."

![dmz](../.res/2022-09-20-09-05-44.png)  

Source: tryhackme

## IP Addresses

- Address to communicate on a network (Layer 3 of OSI model) = routing

| Network class | Network numbers | Network mask | Nb. of networks | No. of hosts per network |
|---------------|-----------------|--------------|-----------------|--------------------------|
| Class A       | 10.0.0.0        | 255.0.0.0    | 126             | 12 646 144               |
| Class B       | 172.16.0.0 to 172.31.0.0 | 255.255.0.0| 16 383 | 65 024                     |
| Class C       | 192.168.0.0 to 192.168.255.255 | 255.255.255.0 | 2 097 151 | 254            |
| Loopback (localhost) | 127.0.0.0 to  127.0.0.7 | 255.255.255.0 | | |

## MAC Addresses

- Media Access Control, Physical address  (Layer 2 of OSI model). Useful to know which device is which.
- It looks like this `00:1a:3f:f1:4c:c6`
- We can lookup the the OUI (3 first octets) to get find out the manufacturer of the device
![image](https://miro.medium.com/max/1200/1*FLrfO7JzkOWSBkPBYly37w.png)
Source: [What is a MAC Address - Lakshan Mamalgaha](https://medium.com/@lakshanmamalgaha/what-is-a-mac-address-and-why-you-should-know-about-it-9f970b3ba3fd)
- Detailed info [here](https://en.wikipedia.org/wiki/MAC_address)

## TCP, UDP and the Three way handshake

### TCP

- Transmission Control Protocol. It is a connection oriented protocol. http, ftp, etc all utilize this.

#### Three way handshake

- TCP uses 3 way handshake
- SYN > SYN ACK > SYN : Asking to connect to a port > Port is open > connection occurs

### UDP

- User Datagram protocol. It is a connectionless protocol. Streaming service, voip etc. all utilize this.

## Common ports and protocol

![image](https://user-images.githubusercontent.com/96747355/175028740-e00258bf-cbcf-4ee5-b2ba-0776caec5c8c.png)  
Source: TCM Security Academy - Course Practical Ethical Hacking  

- TCP
  - FTP = File Transfer protocol
  - SSH and Telnet: connect to a device remotely ssh is encrypted telnet is not
  - SMTP, POP3 and IMAP: related to mail protocol
  - DNS: resolves ip addresses to name
  - HTTP and HTTPS : websites. HTTPS is encrypted
  - SMB: file share (aka SAMBA)
- UDP
  - DHCP: will assign IP address
  - TFTP = trivial FTP
  - SNMP: Simple Network Management Protocol

## The OSI Model

| Number | Layer           | Examples or use |
|--------|-----------------|---------|
| 1      | Physical layer | Data cables |
| 2      | Data | Switches, MAC Addresses |
| 3      | Network | IP addresses, routing |
| 4      | Transport | TCP/UDP |
| 5      | Session | Session management |
| 6      | Presentation | WMV, JPEG, MOV |
| 7      | Application | HTTP, SMTP |

- (Mnemonic: Please Do Not Throw Sausage Pizza Away)

![OSI - TCP/IP](../.res/2022-08-04-15-58-58.png)

Source:TryHackMe

## TCP header (RFC793)

![TCP Header](../.res/2022-08-04-16-01-28.png)
Source:TryHackme

## Subnetting

![image](https://user-images.githubusercontent.com/96747355/175038701-8d9041e0-3021-4f72-a970-2b103ab0b574.png)  
Source: TCM Security Academy  - Course Practical Ethical Hacking  

## End Device Addressing

- IP address - This identifies the host on the network.
- Subnet mask - This is used to identify the network on which the host is connected.
- Default gateway - This identifies the networking device that the host uses to access the internet or another remote network.

## Common ports and protocols

There are two categories of ports, Transmission Control Protocol (TCP), and User Datagram Protocol (UDP).  
TCP is connection-oriented, meaning that a connection between a client and a server must be established before data can be sent. The server must be in a listening state awaiting connection requests from clients.  
UDP utilizes a connectionless communication model. There is no "handshake" and therefore introduces a certain amount of unreliability since there is no guarantee of data delivery. UDP is useful when error correction/checking is either not needed or is handled by the application itself. UDP is suitable for applications that run time-sensitive tasks since dropping packets is faster than waiting for delayed packets due to retransmission, as is the case with TCP and can significantly affect a real-time system. There are 65,535 TCP ports and 65,535 different UDP ports, each denoted by a number.

| Protocol | TCP/UDP Port | Application(s) | Data Security |
|----------|----------|----------------|---------------|
| FTP | 20/21 (TCP) | File Transfer | Cleartext |
|SFTP|22 (TCP) | File Transfer| Encrypted|
|SSH| 22 (TCP)  |Remote Access and File Transfer|Encrypted|
|Telnet|23 (TCP) |Remote Access|Cleartext|
|SMTP|25 (TCP) |Email (MTA)|Cleartext|
| HTTP | 80 (TCP) | Worldwide Web | Cleartext |
|POP3| 110 (TCP) | Email (MDA) | Cleartext|
|IMAP | 143 | Email (MDA) | Cleartext|
|SNMP| 161 (TCP/UDP) ||||
| LDAP| 389 (TCP/UDP)||||
| HTTPS | 443 (TCP)| Worldwide Web| Encrypted|
|SMB| 445 (TCP) ||||
|SMTPS|465|Email (MTA)|Encrypted|
| FTPS | 990 | File Transfer | Encrypted |
|IMAPS| 993 | Email (MDA) | Encrypted|
|POP3S|995| Email (MDA)| Encrypted|
|RDP|3398 (TCP) |RDP||||

- [Common ports MIT](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-sg-en-4/ch-ports.html)
- [Common ports cheat sheet by packetlife](https://packetlife.net/media/library/23/common-ports.pdf)
- [Top 1000 TCP and UDP Port by nullsec](https://nullsec.us/top-1-000-tcp-and-udp-ports-nmap-default/)

## Differenciate active tun connections

- Sometimes when playing with multiple ovpn (I often use htb and thm and do not always reboot my vm) you might end up with 2 tun interface in your `ip add`
- To know which one is the active one you just need to check the routes using `ip route` the one that has more routes is the one that is the current ovpn connection.

## VPN

At a high-level, VPN works by routing our connecting device's internet connection through the target VPN's private server instead of our internet service provider (ISP). When connected to a VPN, data originates from the VPN server rather than our computer and will appear to originate from a public IP address other than our own.  
There are two main types of remote access VPNs: client-based VPN and SSL VPN.  
SSL VPN uses the web browser as the VPN client. The connection is established between the browser and an SSL VPN gateway can be configured to only allow access to web-based applications such as email and intranet sites, or even the internal network but without the need for the end user to install or use any specialized software.  
Client-based VPN requires the use of client software to establish the VPN connection. Once connected, the user's host will work mostly as if it were connected directly to the company network and will be able to access any resources (applications, hosts, subnets, etc.) allowed by the server configuration.  
Some corporate VPNs will provide employees with full access to the internal corporate network, while others will place users on a specific segment reserved for remote workers.

- `ifconfig` or `ip add` we will see a tun adapter if we successfully connected to the VPN
- `netstat -rn` will show us the networks accessible via the VPN.
- `ip -4 a show tun0` check tun0 address
- `ping -c 4 IP.1` (one is the gateway location) 
- We can not be connected from more than 1 device at the same time

*Source Hackthebox Academy*

## Resources

{% embed url="https://skillsforall.com/course/getting-started-cisco-packet-tracer" %} Getting Started with Cisco Packet Tracer (this tool is very fun and useful to use when trying to understand networking) {% endembed %}
{% embed url="https://skillsforall.com/course/networking-essentials" %} Networking Essentials by Cisco {% endembed %}
{% embed url="https://youtu.be/ZxAwQB8TZsM" %} Professor Messer 7 second subnetting {% endembed %}
{% embed url="https://tryhackme.com/room/thelayoftheland" %} TryHackMe - The Lay of the land {% endembed %}