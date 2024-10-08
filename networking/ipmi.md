# IPMI

> *Source CTF and HTB Academy*

- Usually on port 623 UDP.
- [IPMI Basics](https://www.thomas-krenn.com/en/wiki/IPMI_Basics)

Intelligent Platform Management Interface (IPMI) is a set of standardized specifications for hardware-based host management systems used for system management and monitoring. It acts as an autonomous subsystem and works independently of the host's BIOS, CPU, firmware, and underlying operating system. IPMI provides sysadmins with the ability to manage and monitor systems even if they are powered off or in an unresponsive state. It operates using a direct network connection to the system's hardware and does not require access to the operating system via a login shell. IPMI can also be used for remote upgrades to systems without requiring physical access to the target host. IPMI is typically used in three ways:

- Before the OS has booted to modify BIOS settings
- When the host is fully powered down
- Access to a host after a system failure

When not being used for these tasks, IPMI can monitor a range of different things such as system temperature, voltage, fan status, and power supplies. It can also be used for querying inventory information, reviewing hardware logs, and alerting using SNMP. The host system can be powered off, but the IPMI module requires a power source and a LAN connection to work correctly.

## Dangerous Settings

- [Cracking IPMI Password](http://fish2.com/ipmi/remote-pw-cracking.html) with a flaw in the RAKP protocol in IPMI 2.0
- [Retrive IPMI hashses with metasploit `auxiliary/scanner/ipmi/ipmi_dumphashes`](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_dumphashes/)
- `hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u`
- `hashcat -m 7300 ipmi.txt /usr/share/wordlists/rockyou.txt`

## Enumeration

### Nmap

- `sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local`

### Metasploit

- Module: auxiliary/scanner/ipmi/ipmi_version

### Default passwords

During internal penetration tests, we often find BMCs where the administrators have not changed the default password. Some unique default passwords to keep in our cheatsheets include:

|Product|Username|Password|
|-------|--------|--------|
|Dell iDRAC|root|calvin|
|HP iLO|Administrator|randomized 8-character string consisting of numbers and uppercase letters|
|Supermicro IPMI|ADMIN|ADMIN|
