# OSINT Password

## Hunting Breached password

- We can use [dehashed](https://dehashed.com/) **cost money**
  - If a similar password pops multiple times it means it could be used somewhere else.
  - Dehashed will also allow us to lookup for password and give information on where it is coming from
- [WeLeakInfo](https://weleakinfo.to/)
- [Snusbase](https://snusbase.com/)
- [HaveIBeenPwned](https://haveibeenpwned.com/)
- [Scylla](https://scylla.so/)

## Breachparse

- Get the tool [here](https://github.com/hmaverickadams/breach-parse)
- This tool will searched through the breach data and pull down names
- `./breach-parse.sh @domain.com outfile.txt` gather breached emails and passwords from the mentionned domain and put it in a file using the name mentioned
- At the end of the execution we will get 3 files `outfile-master.txt` with email and passwords, `outfile-passwords.txt` with the pulled passwords nd `outfile-users.txt` with the users

## Other tips

- If we get hash:
  - We can try to crack it
  - It can be useful to search it and see if it ties back to something else 
