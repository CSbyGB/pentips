# Nmap

*This documentation has been made from my notes during CTF and pentests and also using the [Network enumeration with NMAP module of HTB Academy](https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap)  
It is really worth taking this course if you have some cubes in your HTB Academy accounts :)*

## Use Cases

- Audit the security aspects of networks
- Simulate penetration tests
- Check firewall and IDS settings and configurations
- Types of possible connections
- Network mapping
- Response analysis
- Identify open ports
- Vulnerability assessment as well.  

> *Source Hackthebox Academy*

## General tips

- Do not neglect UDP scan, you could find interesting services like SNMP

### Recommended methodology

- `sudo nmap -Pn -sV -sC -p- IP-ADR` scan tcp all ports (this command is an example use the one you like best)
- `sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -F --reason` scan top 100 udp ports

## Remove noise when using nmap through proxychains

- Add `quiet_mode` to your proxychains conf file (you should just need to uncomment it usually around line 50 of the file)

## Scan all ports for services and with default script

- `sudo nmap -T4 -sC -O -sV -p- --min-rate=1000 IP-ADR`
- `sudo nmap -T4 -sC -O -sV -p- IP-ADR`
- `sudo nmap -Pn -sV -sC -p- IP-ADR`

## Aggressive scan of all port

- This scan will output OS as if you used `-O`, service versions as with `-sV` and script scanning `-sC`
- `nmap -T4 -A -p- 10.10.10.97` This mode sends a lot more probes, and it is more likely to be detected, but provides a lot of valuable host information

## Put results in a file

- `-oA <filename>` This includes XML output, greppable output, and text output
-`-oN <filename>` Normal output with the .nmap file extension
- `-oG <filename>` Grepable output with the .gnmap file extension
- `-oX <filename>` XML output with the .xml file extension
  - `xsltproc target.xml -o target.html` convert it to html for nice output readable from a browser
- [Nmap Output official documentation](https://nmap.org/book/output.html)

## Host discovery on a network

- `sudo nmap -sn 10.10.110.0-254`
- `sudo nmap 10.129.2.0/24 -sn | grep for | cut -d" " -f5`

## Scan multiple IPs

- `sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20| grep for | cut -d" " -f5`
- `sudo nmap -sn -oA tnet 10.129.2.18-20| grep for | cut -d" " -f5`
- `sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5` from a list

## Other useful scans and tips

- `nmap -v -oG -` See which port are scanned
- `nmap -sV --open <ip address>` This will run a service enumeration (-sV) scan against the default top 1,000 ports and only return open ports (--open).
- The ttl will give us info about the OS, for example `ttl=128` is windows
- `sudo nmap -v -O TARGET-IP` nmap OS Detection Scan
- `sudo nmap -v 192.168.86.39 --script banner.nse` Banner Grab to Enumerate Ports

## UDP Port Scan

- `sudo nmap 10.129.2.28 -F -sU` `-F` will scan the top 100 ports
- `sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -F --reason` `-Pn` Disables ICMP Echo requests, `-n` Disables DNS resolution.

## Nmap Scripts

Do not neglect nmap scripts they can be very useful to push your enumeration to the next level.  
You can specify a script by using the option `--script`. Note that the `-sC` option will perform a scan using the default set of script so it is like doing `--script=default`  

- `--script vuln` will enumerate for known vulnerabilities on the found services in the target
- It also accepts wildcard `*` so say you want all the scripts from a specific category you can do `--script "smb-*"`
- `--script=banner` for banner grabbing
- `--script-trace` trace the progress of NSE scripts. See what command nmap sends what ports are used and what response we receive

|Category|Description|
|--------|-----------|
|auth|Determination of authentication credentials.|
|broadcast|Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans.|
|brute|Executes scripts that try to log in to the respective service by brute-forcing with credentials.|
|default|Default scripts executed by using the -sC option.|
|discovery|Evaluation of accessible services.|
|dos|These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services.|
|exploit|This category of scripts tries to exploit known vulnerabilities for the scanned port.|
|external|Scripts that use external services for further processing.|
|fuzzer|This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.|
|intrusive|Intrusive scripts that could negatively affect the target system.|
|malware|Checks if some malware infects the target system.|
|safe|Defensive scripts that do not perform intrusive and destructive access.|
|version|Extension for service detection.|
|vuln|Identification of specific vulnerabilities.|

> *Source HTB Academy*  

If you want to use your own script you can do so by passing it to nmap this way `--script name-of-your-script.nse`

> [Here](https://iphelix.medium.com/writing-nmap-nse-scripts-for-vulnerability-scanning-69b5a1fb94ae) is a great article on how to write your own nmap script

### Official documentation

- [Nmap Scripting Engine (NSE)](https://nmap.org/book/man-nse.html)
- [NSE Usage and Examples](https://nmap.org/book/nse-usage.html)

## Debug

- If you get this error `Warning: 10.10.64.180 giving up on port because retransmission cap hit (2)`
- Try using `-T4` instead of `-T5`
- Check this post [here](https://stackoverflow.com/questions/14736530/nmap-warning-giving-up-on-port-because-retransmission-cap-hit-2) for more info on this error

## General tips

- Sometimes a port will be `filtered` it can happen if a firewall makes a filter and allow access only from specific addresses.
- `locate scripts/name-of-technology` to find scripts related to a specific technology
- `--top-ports=10` Scans the specified top ports that have been defined as most frequent.
- `--packet-trace` Shows all packets sent and received.
- `-n` Disables DNS resolution.
- `--disable-arp-ping`
- `-sT` uses the TCP three-way handshake to determine if a specific port on a target host is open or closed. The scan sends an SYN packet to the target port and waits for a response. It is considered open if the target port responds with an SYN-ACK packet and closed if it responds with an RST packet. Most accurate way to determine state of a port.

### States

|State|Description|
|-----|-----------|
|open|This indicates that the connection to the scanned port has been established. These connections can be TCP connections, UDP datagrams as well as SCTP associations.|
|closed|When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an RST flag. This scanning method can also be used to determine if our target is alive or not.|
|filtered|Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target.|
|unfiltered|This state of a port only occurs during the TCP-ACK scan and means that the port is accessible, but it cannot be determined whether it is open or closed.|
|open&#124;filtered| If we do not get a response for a specific port, Nmap will set it to that state. This indicates that a firewall or packet filter may protect the port.|
|closed&#124;filtered|This state only occurs in the IP ID idle scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall.|

> *Source HTB Academy*

## Firewall and IDS/IPS Evasion

Nmap's TCP ACK scan (`-sA`) method is much harder to filter for firewalls and IDS/IPS systems than regular SYN (`-sS`) or Connect scans (`-sT`) because they only send a TCP packet with only the ACK flag. When a port is closed or open, the host must respond with an RST flag.  
Unlike outgoing connections, all connection attempts (with the SYN flag) from external networks are usually blocked by firewalls. However, the packets with the ACK flag are often passed by the firewall because the firewall cannot determine whether the connection was first established from the external network or the internal network.  

### Decoy

There are cases in which administrators block specific subnets from different regions in principle. This prevents any access to the target network. Another example is when IPS should block us. For this reason, the Decoy scanning method (`-D`) is the right choice. With this method, Nmap generates various random IP addresses inserted into the IP header to disguise the origin of the packet sent. With this method, we can generate random (`RND`) a specific number (for example: 5) of IP addresses separated by a colon (`:`). Our real IP address is then randomly placed between the generated IP addresses. In the next example, our real IP address is therefore placed in the second position. Another critical point is that the decoys must be alive. Otherwise, the service on the target may be unreachable due to SYN-flooding security mechanisms.  

- `sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5` example using decoy

The spoofed packets are often filtered out by ISPs and routers, even though they come from the same network range. Therefore, we can also specify our VPS servers' IP addresses and use them in combination with "IP ID" manipulation in the IP headers to scan the target.  

Another scenario would be that only individual subnets would not have access to the server's specific services. So we can also manually specify the source IP address (-S) to test if we get better results with this one. Decoys can be used for SYN, ACK, ICMP scans, and OS detection scans. So let us look at such an example and determine which operating system it is most likely to be.

- `sudo nmap 10.129.2.28 -n -Pn -p445 -O` test firewall rule
- `sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0` Scan using different source IP

### DNS Proxying

By default, Nmap performs a reverse DNS resolution unless otherwise specified to find more important information about our target. These DNS queries are also passed in most cases because the given web server is supposed to be found and visited. The DNS queries are made over the UDP port 53. The TCP port 53 was previously only used for the so-called "Zone transfers" between the DNS servers or data transfer larger than 512 bytes. More and more, this is changing due to IPv6 and DNSSEC expansions. These changes cause many DNS requests to be made via TCP port 53.

However, Nmap still gives us a way to specify DNS servers ourselves (`--dns-server <ns>,<ns>`). This method could be fundamental to us if we are in a demilitarized zone (DMZ). The company's DNS servers are usually more trusted than those from the Internet. So, for example, we could use them to interact with the hosts of the internal network. As another example, we can use TCP port 53 as a source port (`--source-port`) for our scans. If the administrator uses the firewall to control this port and does not filter IDS/IPS properly, our TCP packets will be trusted and passed through.

#### SYN-Scan of a Filtered Port

- `sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace`

#### SYN-Scan From DNS Port

- `sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53` (`-n` disables DNS resolution)

#### Connect To The Filtered Port

- `ncat -nv --source-port 53 10.129.2.28 50000`

> *Source HTB Academy*

## Resources

{% embed url="https://nmap.org/book/man.html" %} Nmap Reference Guide {% endembed %}  
{% embed url="https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap" %} Hackthebox Academy - Network Enumeration with NMAP {% endembed %}  
