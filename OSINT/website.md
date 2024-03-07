# OSINT - Website

> *Sources: Hackthebox Academy & TCM Security*

- Check out also the possible tools you can use for website OSINT [here](tools.md#tools-for-website-osint). They are all instalable in you Kali VM some of them are even preinstalled on Kali.

## Search

- Google: `website` or `"website"` or `site:website`
- [Domain Dossier](https://centralops.net/co/)
- [DNSlytics](https://dnslytics.com/reverse-ip)
- [SpyOnWeb](https://spyonweb.com/)
- [Virus Total](https://www.virustotal.com/)
- [Visual Ping](https://visualping.io/)
- [Back Link Watch](http://backlinkwatch.com/index.php) Where your website has been posted
- [View DNS](https://viewdns.info/)
- [Central ops](https://centralops.net/co/)
- [Tiny Scan](https://www.tiny-scan.com) A URL scan tool that provides comprehensive information about any given URL

## Identify Website technology

- [BuiltWith](https://builtwith.com/) identify website technology
- [Wappalyzer](https://www.wappalyzer.com/) browser add on to identify website technology

## Hunting down subdomains

- With google we can look for a specific website using `site:name` and add `inurl:admin` or `inurl:dev` we can also remove subdomain with `-www`
- [Pentest-Tools Subdomain Finder](https://pentest-tools.com/information-gathering/find-subdomains-of-domain#)
- [Spyse](https://spyse.com/)
- [crt.sh](https://crt.sh/) we can use a wildcard to search for example `%.domain.com`
  - `curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .` output results in json
  - `curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u` remove duplicates
  - `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done`
- `dig any inlanefreight.com`
  - **A records**: We recognize the IP addresses that point to a specific (sub)domain through the A record. Here we only see one that we already know.
  - **MX records**: The mail server records show us which mail server is responsible for managing the emails for the company. Since this is handled by google in our case, we should note this and skip it for now.
  - **NS records**: These kinds of records show which name servers are used to resolve the FQDN to IP addresses. Most hosting providers use their own name servers, making it easier to identify the hosting provider.
  - **TXT records**: this type of record often contains verification keys for different third-party providers and other security aspects of DNS, such as SPF, DMARC, and DKIM, which are responsible for verifying and confirming the origin of the emails sent. Here we can already see some valuable information if we look closer at the results.

## Must to use tools

- [Shodan](https://shodan.io) we can use dorks to filter our search with words like `city:` `port:` `org:`
  - `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done` generate a list of IP addresses
  - `for i in $(cat ip-addresses.txt);do shodan host $i;done` run the list through Shodan.
- [Wayback Machine](https://web.archive.org/)

## Methodology for subdomain enum

- We look up with the tools if we can find subdomains
- We check if they are alive with a tool like httpprobe
- We check the subdomains to see what we can do, we can use a tool like Photon that will make screenshot of the list of active subdomains found. This will make our work faster.

## Automate subdomain enumeration

- We can use this code. It was made by Heath Adams on his OSINT course in TCM Security Academy. You can check out this course [here](https://academy.tcm-sec.com/p/osint-fundamentals)
- It will see whois, find subdomains. Once it finds subdomain it is going to check if the subdomains are alive and then it will screenshot the subdomains that are alive.
- It will use automated tools (subfinder, assetfinder, amass, httprobe) that you can find info on [here](tools.md)
- We can make it better and more suited to our needs, like adding other subdomain enum tools, etc..
- There is also a tool called Photon that we can use for similar purposes

```bash
#!/bin/bash

# We want the first argument to be a domain it will be launch like this ./script domain.com
domain=$1
RED="\033[1;31m"
RESET="\033[0m"

info_path=$domain/info
subdomain_path=$domain/subdomains
screenshot_path=$domain/screenshots

# will create all necessary folder for our findings
if [ ! -d "$domain" ];then
    mkdir $domain
fi

if [ ! -d "$info_path" ];then
    mkdir $info_path
fi

if [ ! -d "$subdomain_path" ];then
    mkdir $subdomain_path
fi

if [ ! -d "$screenshot_path" ];then
    mkdir $screenshot_path
fi

echo -e "${RED} [+] Checkin' who it is...${RESET}"
whois $1 > $info_path/whois.txt

echo -e "${RED} [+] Launching subfinder...${RESET}"
subfinder -d $domain > $subdomain_path/found.txt

echo -e "${RED} [+] Running assetfinder...${RESET}"
assetfinder $domain | grep $domain >> $subdomain_path/found.txt

#echo -e "${RED} [+] Running Amass. This could take a while...${RESET}"
#amass enum -d $domain >> $subdomain_path/found.txt

echo -e "${RED} [+] Checking what's alive...${RESET}"
cat $subdomain_path/found.txt | grep $domain | sort -u | httprobe -prefer-https | grep https | sed 's/https\?:\/\///' | tee -a $subdomain_path/alive.txt

echo -e "${RED} [+] Taking dem screenshotz...${RESET}"
gowitness file -f $subdomain_path/alive.txt -P $screenshot_path/ --no-http
```
