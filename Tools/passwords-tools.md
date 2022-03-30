# Passwords, Hashes and wordlist tools

Here is a how to for tools useful for passwords attack or generating wordlists

## Cewl

Cewl can be used to generate a wordlist from a website, this can make you gain a lot of time and it was definitely helpful for me on a lot of CTF ;)  

- `cewl http://website/ > pass.txt`
- `cewl -w pass.txt -m 8 http://website` -m 8 will specify to find words of 8 chars or more

## Username generator

During the enumeration phase we might find users last and first name. Username generator can use thoses to generate usernames.  
- `python3 username_generator.py -w wordlist > users.txt` will send in users.txt a list o usernames generated with a wordlist 

## Crunch

Crunch will generate a word based on criterias you will need.  
- `crunch -h` will show help and options
- Usage: `crunch min max pattern -o outfile`
- `crunch 8 8 01234abcd -o pass.txt` you will get a list of 8 chars word containing the chars in the pattern 01234abcd 
- More advanced options
  - `@` lower case alpha characters
  - `,` upper case alpha characters
  - `%` numeric characters
  - `^` special characters including space
- `crunch 8 8 -t mypass%%` will generate a list of 8 chars word with 2 digits in the end

## Home made script

It can happen for instance that you know the begining of a password or a part of a password, you can use this script to generate 2 to 3 more digits at the end (you can modify it to put it anywhere else in the string)  
And of course we could use crunch as well.
```
# bin/bash
for i in {0..999}
do
 echo "mypassword"$i
done
```

## CUPP

CUPP is a very cool tool that will ask you question about your target in order to generate a personnalized wordlist for your target. It can use different languages  
- `python3 cupp.py -i` launch this to start answering question about the target

## hash-identifier

- `hash-identifier`
- Enter your hash
- You will get a list of possible hashs

## hashid

- `hashid <hash-here>`
- You will get a list of possible hashs

## hashcat

- It is a tool that can help crack hashes using a wordlist.
- `hashcat -h` will print help
- `hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt`
- the flag `-m` will define the hash mode see all modes [here](https://hashcat.net/wiki/doku.php?id=example_hashes)
- You can find also the proper module using this `hashcat --help | grep <service-you-need>` See the example below
  ```
  ┌──(root💀kali)-[~/active-directory]
  └─# hashcat --help | grep Kerberos
   7500 | Kerberos 5, etype 23, AS-REQ Pre-Auth            | Network Protocols
  13100 | Kerberos 5, etype 23, TGS-REP                    | Network Protocols
  18200 | Kerberos 5, etype 23, AS-REP                     | Network Protocols
  19600 | Kerberos 5, etype 17, TGS-REP                    | Network Protocols
  19700 | Kerberos 5, etype 18, TGS-REP                    | Network Protocols
  19800 | Kerberos 5, etype 17, Pre-Auth                   | Network Protocols
  19900 | Kerberos 5, etype 18, Pre-Auth                   | Network Protocols
  ```
- the flag `-a` will set the attack mode
- We can either give it a single hash or a file
- Adding `--show` will show the crack value if the hash is cracked
- We can use it for Brute-force attack: `-a 3` will specify it is bruteforce `?d?d?d` will use 3 digits from 000 to 999.
- Example: `hashcat -a 3 -m 0 HASH-HERE ?d?d?d`

### Using hashcat with rules

- In kali rules are located in `/usr/share/hashcat/rules`
- `hashcat -a 0 -m 1000 <HASH-HERE> -r /usr/share/hashcat/rules/OneRuleToRuleThemAll.rule /usr/share/wordlists/rockyou.txt`

## John the Ripper

### Rule-Based Attack

- John the ripper has a config file that contains rule sets, which is located at /etc/john/john.conf or /opt/john/john.conf depending on your distro or how john was installed. You can read /etc/john/john.conf and look for List.Rules to see all the available rules:  
`cat /etc/john/john.conf|grep "List.Rules:" | cut -d"." -f3 | cut -d":" -f2 | cut -d"]" -f1 | awk NF`  
- We can create a rule and add it to the conf file, for example this rule will add a symbol at the beginning of the word and a number at the end: 
  ```
  [List.Rules:My-own-rule]
  Az"[0-9]" ^[!@#$]
  ```

## Hydra

- Hydra is a very handy tool for when you need to bruteforce auth. It supports a lot of protocols.

### SSH

- `hydra -L users -P pass 10.10.4.129 -t 4 ssh ` will use the user list and password list mentioned if you put minus l or minus p you can just specify one user or one password.

### FTP

- It is pretty much the same for ftp except you need to add ftp instead of ssh in the end: `hydra -L users -P pass 10.10.4.129 -t 4 ftp`

## Resources

{% embed url="https://github.com/digininja/CeWL" %} CeWL {% endembed %}
{% embed url="https://github.com/shroudri/username_generator" %} Username Generator {% endembed %}
{% embed url="https://www.kali.org/tools/crunch/" %} Crunch {% endembed %}
{% embed url="https://github.com/Mebus/cupp" %} CUPP - Common User Passwords Profiler {% endembed %}
{% embed url="https://www.kali.org/tools/hash-identifier/" %} hash-identifier on kali {% endembed %}
