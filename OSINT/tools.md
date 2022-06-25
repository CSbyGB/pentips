# OSINT Tools

- In this part you will find tools installable in your machine or VM to perform some OSINT task

## Exiftool

- Tool to get information from a file (image or pdf)

### Install

- Install on kali: `sudo apt install libimage-exiftool-perl`

### Use

- `exiftool filename`

## The Harverster

- Tool to hunt emals and breached data
- Preinstalled on kali linux

### Use

- `theHarverster -d domain.com -b all` will get infos about domain.com from all search engines available with the tool.
- Can be combined with other tools such as:
  - [breach-parse](https://github.com/hmaverickadams/breach-parse)
  - h8mail

## Tools for username and Account OSINT

### whatsmyname

#### Install

- `git clone https://github.com/WebBreacher/WhatsMyName.git`
- `cd WhatsMyName`

#### Use

- `python3 web_accounts_list_checker.py -u username`

### Sherlock

#### Install

- `sudo apt install sherlock`

#### Use

- `sherlock username`

## phoneinfoga

- A tool to osint phone number

### Install

- `curl -sSL https://raw.githubusercontent.com/sundowndev/phoneinfoga/master/support/scripts/install | bash`
- `tar -xf phoneinfoga_Linux_x86_64.tar.gz`

### Use

- `phoneinfoga serve -p 8080` will serve the gui on port 8080 then you will just have to go to http://localhost:8080 and make a research
- `phoneinfoga scan -n number` you will need to specify the cuntry code in front of the number for example for US or Canada you need to put 1

## Twint

- Tool for Twitter OSINT available [here](https://github.com/twintproject/twint).

## Use

- Upgrade
  - `pip3 install --upgrade -e git+https://github.com/twintproject/twint.git@origin/master#egg=twint`
  - `pip3 install --upgrade aiohttp_socks`
- `twint -u username`
- `twint -u username -s keyword`
- Lots of other possibilities it is worth reading the doc

## Tools for website OSINT

### Identifying website technology

#### Wappalyzer

- We can use the browser add on [Wappalyzer](https://www.wappalyzer.com/) to see the technologies used on the website

#### Whatweb

- It is preinstalled on Kali. You can find the githb page [here](https://github.com/urbanadventurer/WhatWeb)
- `whatweb webiste.com`

### Hunting subdomain

#### Sublist3r

- Tool to find subdomains. See about it [here](https://github.com/aboul3la/Sublist3r)
- `apt install sublist3r` Install it
- 

#### Subfinder

- Tool to find subdomains
- Available [here](https://github.com/projectdiscovery/subfinder)
- `subfinder -d domain`

#### Assetfinder

- Another tool to find subdomains
- Available [here](https://github.com/tomnomnom/assetfinder)
- `assetfinder domain`

#### Amass

- Tool for subdomain enumeration
- Available [here](https://github.com/OWASP/Amass)
- `amass enum -d domain`

#### httprobe

- After finding multiple subdomains we can use httprobe to check if they are alive or not
- Find httprobe [here](https://github.com/tomnomnom/httprobe)
- We could use a command like this `cat findings.txt | sort -u | httprobe -s -p https:443` we can limit our results to port 443
- We can put our result in a file named `alive-findings.txt` (we then need to strip `https://`, `http://` and `:443` and use it in gowitness

#### Gowitness

- We can also go through our findings and get screenshots of them using gowitness
- Find GoWitness [here](https://github.com/sensepost/gowitness/wiki/Installation)
- `gowitness file -f ./alive-findings.txt -P ./screenshots --no-http` this command will go through every finding and make a screenshot

## OSINT Frameworks

### Recon-ng

- Find it [here](https://github.com/lanmaster53/recon-ng/wiki) along with some documentation
- `recon-ng`
- `marketplace search` see all available tools
- `marketplace install tool` install one of the tool from the market (some of them require API keys)
- `modules load tool` load the tool just installed
- `info` to see what we can do with the module
- `options set ITEM setting` to set something in the module for instance if we were playing with hackertarget we could do `options set SOURCE domain.com`
- `run` to run the module
- Some nice module on recon-ng are hackertarget (OSINT on website such as subdomain enum and ip adr finder), profiler (search for accounts with a specific userame on different websites)

### Maltego

- Preinstalled on kali
- Run for free register and account confirm it
- We will need api keys for most of the modules
- We can use it without modules also
- We can make a new graph domain for instance if we want to make website OSINT

## Hunchly

- Paid tools but free trial possible. Only runs on google chrome.
- Find Hunchly [here](https://hunch.ly)
- We can launch new case and keep them in our dashboard
- We can start the "tracking" and add it to a specific case
- We can highlight keywords, take notes on website
- It will record everything viewed

