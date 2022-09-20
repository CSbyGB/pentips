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

| Protocol | TCP Port | Application(s) | Data Security |
|----------|----------|----------------|---------------|
| FTP | 21 | File Transfer | Cleartext |
| FTPS | 990 | File Transfer | Encrypted |
| HTTP | 80 | Worldwide Web | Cleartext |
| HTTPS | 443| Worldwide Web| Encrypted|
|IMAP | 143 | Email (MDA) | Cleartext|
|IMAPS| 993 | Email (MDA) | Encrypted|
|POP3| 110 | Email (MDA) | Cleartext|
|POP3S|995| Email (MDA)| Encrypted|
|SFTP|22 | File Transfer| Encrypted
|SSH| 22  |Remote Access and File Transfer|Encrypted|
|SMTP|25|Email (MTA)|Cleartext|
|SMTPS|465|Email (MTA)|Encrypted|
|Telnet|23|Remote Access|Cleartext|

## Differenciate active tun connections

- Sometimes when playing with multiple ovpn (I often use htb and thm and do not always reboot my vm) you might end up with 2 tun interface in your `ip add`
- To know which one is the active one you just need to check the routes using `ip route` the one that has more routes is the one that is the current ovpn connection.

## Resources

{% embed url="https://skillsforall.com/course/getting-started-cisco-packet-tracer" %} Getting Started with Cisco Packet Tracer (this tool is very fun and useful to use when trying to understand networking) {% endembed %}
{% embed url="https://skillsforall.com/course/networking-essentials" %} Networking Essentials by Cisco {% endembed %}
{% embed url="https://youtu.be/ZxAwQB8TZsM" %} Professor Messer 7 second subnetting {% endembed %}
{% embed url="https://tryhackme.com/room/thelayoftheland" %} TryHackMe - The Lay of the land {% endembed %}