# DNS

> *Source CTF and HTB Academy*

- Usually on port 53

Domain Name System (DNS) is an integral part of the Internet.  
DNS is a system for resolving computer names into IP addresses, and it does not have a central database.

|Server Type|Description|
|-----------|-----------|
|DNS Root Server|The root servers of the DNS are responsible for the top-level domains (TLD). As the last instance, they are only requested if the name server does not respond. Thus, a root server is a central interface between users and content on the Internet, as it links domain and IP address. The Internet Corporation for Assigned Names and Numbers (ICANN) coordinates the work of the root name servers. There are 13 such root servers around the globe.|
|Authoritative Nameserver|Authoritative name servers hold authority for a particular zone. They only answer queries from their area of responsibility, and their information is binding. If an authoritative name server cannot answer a client's query, the root name server takes over at that point.|
|Non-authoritative Nameserver|Non-authoritative name servers are not responsible for a particular DNS zone. Instead, they collect information on specific DNS zones themselves, which is done using recursive or iterative DNS querying.|
|Caching DNS Server|Caching DNS servers cache information from other name servers for a specified period. The authoritative name server determines the duration of this storage.|
|Forwarding Server|Forwarding servers perform only one function: they forward DNS queries to another DNS server.|
|Resolver|Resolvers are not authoritative DNS servers but perform name resolution locally in the computer or router.|

## DNS records

|DNS Record|Description|
|----------|-----------|
|A|Returns an IPv4 address of the requested domain as a result.|
|AAAA|Returns an IPv6 address of the requested domain.|
|MX|Returns the responsible mail servers as a result.|
|NS|Returns the DNS servers (nameservers) of the domain.|
|TXT|This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.|
|CNAME|This record serves as an alias. If the domain www.hackthebox.eu should point to the same IP, and we create an A record for one and a CNAME record for the other.|
|PTR|The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.|
|SOA|Provides information about the corresponding DNS zone and email address of the administrative contact.|

- `dig soa www.inlanefreight.com`

> The dot (.) is replaced by an at sign (@) in the email address

## Default Configuration

### Local DNS configuration

For Bind9 DNS server: usually `named.conf.local`, `named.conf.options`, `named.conf.log`.

- `cat /etc/bind/named.conf.local`

A zone file is a text file that describes a DNS zone with the BIND file format. In other words it is a point of delegation in the DNS tree. The BIND file format is the industry-preferred zone file format and is now well established in DNS server software. A zone file describes a zone completely.  
For the IP address to be resolved from the Fully Qualified Domain Name (FQDN), the DNS server must have a reverse lookup file. In this file, the computer name (FQDN) is assigned to the last octet of an IP address, which corresponds to the respective host, using a PTR record. The PTR records are responsible for the reverse translation of IP addresses into names, as we have already seen in the above table.  

- `cat /etc/bind/db.domain.com` Zone file
- `cat /etc/bind/db.10.129.14` Reverse Name Resolution Zone Files

## Dangerous Settings

|Option|Description|
|------|-----------|
|allow-query|Defines which hosts are allowed to send requests to the DNS server.|
|allow-recursion|Defines which hosts are allowed to send recursive requests to the DNS server.|
|allow-transfer|Defines which hosts are allowed to receive zone transfers from the DNS server.|
|zone-statistics|Collects statistical data of zones.|

> Since nearly all network applications use DNS, attacks against DNS servers represent one of the most prevalent and significant threats today.

## Enumeration

### Nmap

- `sudo nmap -T4 -sC -O -sV -p 53 --script dns-nsid 10.129.42.195`

## Dig

- `dig ns inlanefreight.htb @10.129.14.128` which other name servers are known
- `dig CH TXT version.bind 10.129.120.85` query a DNS server's version
- `dig any inlanefreight.htb @10.129.14.128` option ANY to view all available records
- `dig a inlanefreight.htb @10.129.38.215`
- `dig txt inlanefreight.htb @10.129.38.215`
- `dig mx inlanefreight.htb @10.129.38.215`

Zone transfer refers to the transfer of zones to another server in DNS, which generally happens over TCP port 53. This procedure is abbreviated Asynchronous Full Transfer Zone (AXFR). Since a DNS failure usually has severe consequences for a company, the zone file is almost invariably kept identical on several name servers.

- `dig axfr inlanefreight.htb @10.129.14.128`
- `dig axfr internal.inlanefreight.htb @10.129.14.128`  

DIG - AXFR Zone Transfer
- `dig AXFR @ns1.inlanefreight.htb inlanefreight.htb`

Tools like [Fierce](https://github.com/mschwager/fierce) can also be used to enumerate all DNS servers of the root domain and scan for a DNS zone transfer:  

- `fierce --domain zonetransfer.me`

## Subdomain bruteforce

- `for sub in $(cat /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done`

- [Subfinder](https://github.com/projectdiscovery/subfinder)
- [DNSdumpster](https://dnsdumpster.com/)
- [Sublist3r](https://github.com/aboul3la/Sublist3r)

```bash
./subfinder -d inlanefreight.com -v
```

- [subbrute](https://github.com/TheRook/subbrute)

```bash
git clone https://github.com/TheRook/subbrute.git >> /dev/null 2>&1
cd subbrute
echo "ns1.inlanefreight.com" > ./resolvers.txt
./subbrute inlanefreight.com -s ./names.txt -r ./resolvers.txt
```

### DNSenum

- [Github repo](https://github.com/fwaeytens/dnsenum)
- `dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb`

> Do not forget to use the latest command on subdomains as well

## DNS Spoofing

DNS spoofing is also referred to as DNS Cache Poisoning. This attack involves altering legitimate DNS records with false information so that they can be used to redirect online traffic to a fraudulent website. Example attack paths for the DNS Cache Poisoning are as follows:

- An attacker could intercept the communication between a user and a DNS server to route the user to a fraudulent destination instead of a legitimate one by performing a Man-in-the-Middle (MITM) attack.

- Exploiting a vulnerability found in a DNS server could yield control over the server by an attacker to modify the DNS records.

From a local network perspective, an attacker can also perform DNS Cache Poisoning using MITM tools like [Ettercap](https://www.ettercap-project.org/) or [Bettercap](https://www.bettercap.org/).

## Resources

{% embed url="https://securitytrails.com/blog/most-popular-types-dns-attacks" %} Most popular attacks on DNS {% endembed %}  
