# Passwords and wordlist tools

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

## Resources

- [CeWL](https://github.com/digininja/CeWL)
- [Username Generator](https://github.com/shroudri/username_generator)
- [Crunch](https://www.kali.org/tools/crunch/)
- [CUPP - Common User Passwords Profiler](https://github.com/Mebus/cupp)
