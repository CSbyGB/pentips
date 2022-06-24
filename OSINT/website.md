# OSINT - Website

- Check out also the possible tools you can use for website OSINT [here](tools.md#tools-for-website-osint)

## Search

- Google: `website` or `"website"` or `site:website`
- [BuiltWith](https://builtwith.com/)
- [Domain Dossier](https://centralops.net/co/)
- [DNSlytics](https://dnslytics.com/reverse-ip)
- [SpyOnWeb](https://spyonweb.com/)
- [Virus Total](https://www.virustotal.com/)
- [Visual Ping](https://visualping.io/)
- [Back Link Watch](http://backlinkwatch.com/index.php) Where your website has been posted
- [View DNS](https://viewdns.info/)
- [Central ops](https://centralops.net/co/)

## Hunting down subdomains

- With google we can look for a specific website using `site:name` and add `inurl:admin` or `inurl:dev` we can also remove subdomain with `-www`
- [Pentest-Tools Subdomain Finder](https://pentest-tools.com/information-gathering/find-subdomains-of-domain#)
- [Spyse](https://spyse.com/)
- [crt.sh](https://crt.sh/)

## Must to use tools

- [Shodan](https://shodan.io) we can use dorks to filter our search with words like `city:` `port:` `org:`
- [Wayback Machine](https://web.archive.org/)

## Automate website OSINT

- We can use this code. It was made by Heath Adams on his OSINT course. You can check out this course here(https://academy.tcm-sec.com/p/osint-fundamentals)
- It will see whois, find subdomains. Once it finds subdomain it is going to check if the subdomains are alive and then it will screenshot the subdomains that are alive.
- It will use automated tools (subfinder, assetfinder, amass, httprobe) that you can find info on [here](tools.md)
- We can make it better and more suited to our needs, like adding other subdomain enum tools, etc..
- Thre is also a tool called Photon that we can use for similar purposes
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
