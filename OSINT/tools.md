# OSINT Tools

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
