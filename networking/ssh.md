# SSH

> *Source CTF and HTB Academy*

- Usually port 22

Secure Shell (SSH) is a network protocol that runs on port 22 by default and provides users such as system administrators a secure way to access a computer remotely. SSH can be configured with password authentication or passwordless using public-key authentication using an SSH public/private key pair. SSH can be used to remotely access systems on the same network, over the internet, facilitate connections to resources in other networks using port forwarding/proxying, and upload/download files to and from remote systems.  

SSH uses a client-server model, connecting a user running an SSH client application such as OpenSSH to an SSH server. While attacking a box or during a real-world assessment, we often obtain cleartext credentials or an SSH private key that can be leveraged to connect directly to a system via SSH. An SSH connection is typically much more stable than a reverse shell connection and can often be used as a "jump host" to enumerate and attack other hosts in the network, transfer tools, set up persistence, etc. If we obtain a set of credentials, we can use SSH to login remotely to the server by using the username @ the remote server IP, as follows:
`ssh user@10.10.10.10`

- [Openssh](https://www.openssh.com/)

## Authentication method

- Password authentication
- Public-key authentication
- Host-based authentication
- Keyboard authentication
- Challenge-response authentication
- GSSAPI authentication
- [6 SSH authentication methods to secure connection on GoLinuxCloud](https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/)

## Default config

- `cat /etc/ssh/sshd_config  | grep -v "#" | sed -r '/^\s*$/d'`
- [sshd_config on ssh.com](https://www.ssh.com/academy/ssh/sshd_config)

## Dangerous Settings

|Setting|Description|
|-------|-----------|
|PasswordAuthentication yes|Allows password-based authentication.|
|PermitEmptyPasswords yes|Allows the use of empty passwords.|
|PermitRootLogin yes|Allows to log in as the root user.|
|Protocol 1|Uses an outdated version of encryption.|
|X11Forwarding yes|Allows X11 forwarding for GUI applications.|
|AllowTcpForwarding yes|Allows forwarding of TCP ports.|
|PermitTunnel|Allows tunneling.|
|DebianBanner yes|Displays a specific banner when logging in.|

## Enumeration

- We need to keep notes of the ssh version

### ssh cmd

- We can try to ssh to our target `ssh IP-ADD`. In our example we get this  
![image](https://user-images.githubusercontent.com/96747355/175833787-a2b8bcbd-05a9-4ecb-ac37-b584c896253c.png)  
- For this error we can try this `ssh 10.0.2.4 -oKexAlgorithms=+diffie-hellman-group1-sha1`  
![image](https://user-images.githubusercontent.com/96747355/175834000-bc2e9cbe-5949-4836-88d7-c9d399f95054.png)  
- For this error we can try this `ssh 10.0.2.4 -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa`  
![image](https://user-images.githubusercontent.com/96747355/175834033-1c9a18f8-bb6f-4961-bbc1-3550da4dba45.png)  
- Finally for this error we can use this and will try to connect `ssh 10.0.2.4 -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa -c aes128-cbc`

## SSH-Audit

- `git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit`
- `./ssh-audit.py 10.129.14.132`

## Nmap

- `nmap -p22 TARGET-IP --script ssh-auth-methods --script-args="ssh.user=username"` will show authentication methods (requires a username).  
See output example

```bash
PORT   STATE SERVICE
22/tcp open  ssh
| ssh-auth-methods: 
|   Supported authentication methods: 
|_    publickey
```

- `nmap -p22 TARGET-IP --script ssh-hostkey --script-args ssh_hostkey=full`
See output example

```bash
PORT   STATE SERVICE
22/tcp open  ssh
| ssh-hostkey: 
|   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMCwfnXf0WI8xwEChpVHr9JZNlgJxXnGbrVM7TkTx2Kh+bnYBwtuZrIBj7zD+LNRqIOHPMmDCZuVHOONRX9qauAq46EtCYBN35NtCtQnBRGPMC8fVxPk6KORkrWJ2J5c/crYnNCbVOt55fad739S1fYs35+X2As5/bR+F6zfnpsTMvNSiXzzJRb4C/W4PcQ9T3Az7knI+8oyP4WsbUN3l2KOq+QsWscv5Ida+ZTR7DJIbfFs/fdsPzJsLJsONsjOwyOmWsge/nik2zMRkuIUgrYco8MtPoKKfXohpFffUm4dx0I54wv9GiIHRjEEx3przciF6XvPq/2uPWhi1wpn9R
|   ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJjL31haOqBjuQ4XE/yrVby9ygrWlBMaGhxa2gzUau6Oxqp+Lomi72wf/KQ1/FPwG8qFGM0mJxTFKnwj/Ez5Ok0=
|_  ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAII61CNVnXxys5WNU/Q2WShh2JKJb3Pd1sPItUTK144ZJ
```

- `nmap -p22 TARGET-IP --script ssh2-enum-algos`

## Change Authentication Method

- `ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password`

## Password spray or bruteforce

- `hydra -L users.txt -P passwords.txt TARGET-IP -t 4 ssh`
- `hydra -L user.list -P password.list ssh://10.129.42.197`

## Connect to ssh server

- `ssh username@IP-TARGET`

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ssh" %} Pentesting SSH - Hacktricks {% endembed %}  
