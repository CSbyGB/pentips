# NFS

> *Source CTF and HTB Academy*

- TCP and UDP ports 2049 or 111

Network File System (NFS) is a network file system developed by Sun Microsystems and has the same purpose as SMB. Its purpose is to access file systems over a network as if they were local. However, it uses an entirely different protocol.

## Default configuration

- `cat /etc/exports`

|Option|Description|
|------|-----------|
|rw|Read and write permissions.|
|ro|Read only permissions.|
|sync|Synchronous data transfer. (A bit slower)|
|async|Asynchronous data transfer. (A bit faster)|
|secure|Ports above 1024 will not be used.|
|insecure|Ports above 1024 will be used.|
|no_subtree_check|This option disables the checking of subdirectory trees.|
|root_squash|Assigns all permissions to files of root UID/GID 0 to the UID/GID of anonymous, which prevents root from accessing files on an NFS mount.|

## Dangerous settings

|rw|Read and write permissions.|
|--|---------------------------|
|insecure|Ports above 1024 will be used.|
|nohide|If another file system was mounted below an exported directory, this directory is exported by its own exports entry.|
|no_root_squash|All files created by root are kept with the UID/GID 0.|

## Enumeration

### Nmap

- `sudo nmap 10.129.14.128 -p111,2049 -sV -sC`
- `sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049`

### Mount an NFS service

- `sudo apt install nfs-common` install showmount
- `showmount -e 10.129.14.128` show availables NFS shares
- `mkdir target-NFS` create a directory to mount our target
- Mount
  - `sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock` mount our target in our newly created dir
  - `cd target-NFS`
  - `tree .`
  - `ls -l mnt/nfs/` List Contents with Usernames & Group Names
  - `ls -n mnt/nfs/` List Contents with UIDs & GUIDs
- Unmount
  - `cd ..`
  - `sudo umount ./target-NFS`

We can also use NFS for further escalation. For example, if we have access to the system via SSH and want to read files from another folder that a specific user can read, we would need to upload a shell to the NFS share that has the SUID of that user and then run the shell via the SSH user.
