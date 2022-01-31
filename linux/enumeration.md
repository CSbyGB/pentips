# Linux Enumeration

## Useful commands

- `hostname` returns the hostame of the target machine
- `uname -a` print system information (kernel)
- `/proc/version` information about the target system processes
- `/etc/issue` contains some information about the operating system
- `ps` list the running processes
    - ps -A: View all running processes
    - ps axjf: View process tree
    - ps aux: Show processes for all users (a), Display the user that launched the process (u), Show processes that are not attached to a terminal (x)
- `env` show environmental variables
- `sudo -l` list all commands the user can run using `sudo`
- `ls -la` list files with rights and hidden files
- ìd` overview of the user’s privilege level and group memberships
- `/etc/passwd` list all the users
    - pipe it to cut `cat /etc/passwd | cut -d ":" -f 1` we will get the first col so usernames only (can be system or service users)
    - pipe it to grep on home `cat /etc/passwd | grep home` should show users only
- `history` will show earlier commands

## Scripts for enumerations

## Resources
- [gtfobins](https://gtfobins.github.io/)
