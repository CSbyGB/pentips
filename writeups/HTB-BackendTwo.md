# Hackthebox BackendTwo - Linux

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T5 -sC -sV -O -Pn -p- 10.10.11.162                                                                                                                                                                                           130 ⨯
Nmap scan report for 10.10.11.162
Host is up (0.023s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|_  256 22:21:e9:f4:85:90:87:45:16:1f:73:36:41:ee:3b:32 (ED25519)
80/tcp open  http    uvicorn
|_http-server-header: uvicorn
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     content-type: text/plain; charset=utf-8
|     Connection: close
|     Invalid HTTP request received.
|   FourOhFourRequest: 
|     HTTP/1.1 404 Not Found
|     date: Sat, 14 May 2022 23:59:13 GMT
|     server: uvicorn
|     content-length: 22
|     content-type: application/json
|     Connection: close
|     {"detail":"Not Found"}
|   GetRequest: 
|     HTTP/1.1 200 OK
|     date: Sat, 14 May 2022 23:59:02 GMT
|     server: uvicorn
|     content-length: 22
|     content-type: application/json
|     Connection: close
|     {"msg":"UHC Api v2.0"}
|   HTTPOptions: 
|     HTTP/1.1 405 Method Not Allowed
|     date: Sat, 14 May 2022 23:59:08 GMT
|     server: uvicorn
|     content-length: 31
|     content-type: application/json
|     Connection: close
|_    {"detail":"Method Not Allowed"}
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port80-TCP:V=7.92%I=7%D=5/14%Time=6280407F%P=x86_64-pc-linux-gnu%r(GetR
SF:equest,A6,"HTTP/1\.1\x20200\x20OK\r\ndate:\x20Sat,\x2014\x20May\x202022
SF:\x2023:59:02\x20GMT\r\nserver:\x20uvicorn\r\ncontent-length:\x2022\r\nc
SF:ontent-type:\x20application/json\r\nConnection:\x20close\r\n\r\n{\"msg\
SF:":\"UHC\x20Api\x20v2\.0\"}")%r(HTTPOptions,BF,"HTTP/1\.1\x20405\x20Meth
SF:od\x20Not\x20Allowed\r\ndate:\x20Sat,\x2014\x20May\x202022\x2023:59:08\
SF:x20GMT\r\nserver:\x20uvicorn\r\ncontent-length:\x2031\r\ncontent-type:\
SF:x20application/json\r\nConnection:\x20close\r\n\r\n{\"detail\":\"Method
SF:\x20Not\x20Allowed\"}")%r(RTSPRequest,76,"HTTP/1\.1\x20400\x20Bad\x20Re
SF:quest\r\ncontent-type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x
SF:20close\r\n\r\nInvalid\x20HTTP\x20request\x20received\.")%r(FourOhFourR
SF:equest,AD,"HTTP/1\.1\x20404\x20Not\x20Found\r\ndate:\x20Sat,\x2014\x20M
SF:ay\x202022\x2023:59:13\x20GMT\r\nserver:\x20uvicorn\r\ncontent-length:\
SF:x2022\r\ncontent-type:\x20application/json\r\nConnection:\x20close\r\n\
SF:r\n{\"detail\":\"Not\x20Found\"}")%r(GenericLines,76,"HTTP/1\.1\x20400\
SF:x20Bad\x20Request\r\ncontent-type:\x20text/plain;\x20charset=utf-8\r\nC
SF:onnection:\x20close\r\n\r\nInvalid\x20HTTP\x20request\x20received\.")%r
SF:(DNSVersionBindReqTCP,76,"HTTP/1\.1\x20400\x20Bad\x20Request\r\ncontent
SF:-type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\nI
SF:nvalid\x20HTTP\x20request\x20received\.")%r(DNSStatusRequestTCP,76,"HTT
SF:P/1\.1\x20400\x20Bad\x20Request\r\ncontent-type:\x20text/plain;\x20char
SF:set=utf-8\r\nConnection:\x20close\r\n\r\nInvalid\x20HTTP\x20request\x20
SF:received\.")%r(SSLSessionReq,76,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n
SF:content-type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r
SF:\n\r\nInvalid\x20HTTP\x20request\x20received\.")%r(TerminalServerCookie
SF:,76,"HTTP/1\.1\x20400\x20Bad\x20Request\r\ncontent-type:\x20text/plain;
SF:\x20charset=utf-8\r\nConnection:\x20close\r\n\r\nInvalid\x20HTTP\x20req
SF:uest\x20received\.")%r(TLSSessionReq,76,"HTTP/1\.1\x20400\x20Bad\x20Req
SF:uest\r\ncontent-type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x2
SF:0close\r\n\r\nInvalid\x20HTTP\x20request\x20received\.");
Aggressive OS guesses: Linux 4.15 - 5.6 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.3 (95%), Linux 3.1 (95%), Linux 3.2 (95%), Linux 5.3 - 5.4 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 201.13 seconds
```

## Port 80

### Gobuster

```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.11.162/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/api/objects.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.11.162/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/api/objects.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/05/14 19:59:58 Starting gobuster in directory enumeration mode
===============================================================
/api                  (Status: 200) [Size: 19]
/docs                 (Status: 401) [Size: 30]
                                              
===============================================================
2022/05/14 20:00:20 Finished
===============================================================

```
![image](https://user-images.githubusercontent.com/96747355/168451914-367039ca-906e-4001-9997-4e1f980a6138.png)  
- Just like with the first backend box we can enumerate users with the id  
![image](https://user-images.githubusercontent.com/96747355/168451941-6c4d94a1-eedd-47aa-a220-9d1f683b528e.png)  
- `http://10.10.11.162/api/v1/admin/` requires authentication
- `http://10.10.11.162/docs` requires auth

### Wfuzz

Let's fuzz with wfuzz and http verbs to see what we could do
```
┌──(root💀kali)-[~]
└─# wfuzz -X POST -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://10.10.11.162/api/v1/user/FUZZ --hc 404,405                                                                                               130 ⨯
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.11.162/api/v1/user/FUZZ
Total requests: 4702

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                     
=====================================================================

000001029:   307        0 L      0 W        0 Ch        "cgi-bin/"                                                                                                                                                                  
000002500:   422        0 L      3 W        172 Ch      "login"                                                                                                                                                                     
000003788:   422        0 L      2 W        81 Ch       "signup"                            
```

- We are able to create a user (same process as the previous backend box)  
![image](https://user-images.githubusercontent.com/96747355/168452666-637ac48e-97af-49c9-9172-cc268b67232a.png)  
- We can now login using the user and we get a bearer token  
![image](https://user-images.githubusercontent.com/96747355/168452685-d6e0a0ab-4c64-4822-aaf5-d3a099c2f332.png)
- Then we can access the swager with intercept on we add our token bearer and set the cntent type to json  
![image](https://user-images.githubusercontent.com/96747355/168452696-0f63f3bf-30b9-43cd-9b66-8308bceec24a.png)  
- We get redirected to openapi.json  
![image](https://user-images.githubusercontent.com/96747355/168452715-ccb9ead4-434c-4c22-8fac-7e2e04c457b2.png)  
- We have a user flag request in the swagger let's try to grab it  
- we need to be admin to grab it
- Also we can enumerate users with burp intruder  
![image](https://user-images.githubusercontent.com/96747355/168453079-b98e6639-bc94-4094-a6c2-a4ccabaf6d28.png)  
- We get 11 users + 1 (the one we created)
- We are also able to see other profile we are player: `"profile":"UHC Player"`, there is also a guest `"profile":"UHC Guest",` and of course the Admin (the first one we found)
- In the swagger we can actually edit a profile. Let's try to make ourselves admin. It works!  
![image](https://user-images.githubusercontent.com/96747355/168453192-954bff24-4abe-4e60-9c0c-1c6100469c78.png)
- If we check we are indeed admin  
![image](https://user-images.githubusercontent.com/96747355/168453210-0e04fa0f-b414-4f9f-8f7a-4152f9d31fc2.png)  
- However we can see that we are not super, let's try to put ourselves the is super user to true with the put request to edit  
![image](https://user-images.githubusercontent.com/96747355/168453243-51fbfcc3-4965-44bb-acb8-6ea7b7b96c70.png)  
- And it worked too  
![image](https://user-images.githubusercontent.com/96747355/168453254-6e063abf-8568-4eb6-a066-594ada93117a.png)
- We have to login again and generate a new token, let's replay our initial login request
![image](https://user-images.githubusercontent.com/96747355/168453275-f29e89f4-8b40-4854-873c-0240cca35c5b.png)
- Let's try to grab the flag again with this new token. It works  
![image](https://user-images.githubusercontent.com/96747355/168453321-227a6386-9a5c-47f2-9b7c-14147221db60.png)
- Let's now play with the read file and write file request we can see in the swagger to get a file the name needs to be in base64 url as mentioned in the swagger
- We are able to get /etc/passwd this way  
![image](https://user-images.githubusercontent.com/96747355/168453703-196f1041-e5a0-4938-a083-62adad75f764.png)  
- Let's try to get the main.py it should be here `/home/htb/app/main.py`
```
import asyncio\nimport os\n\nwith open('pid','w') as f:\n    f.write( str(os.getpid())  )\n\nfrom fastapi import FastAPI, APIRouter, Query, HTTPException, Request, Depends\nfrom fastapi_contrib.common.responses import UJSONResponse\nfrom fastapi import FastAPI, Depends, HTTPException, status\nfrom fastapi.security import HTTPBasic, HTTPBasicCredentials\nfrom fastapi.openapi.docs import get_swagger_ui_html\nfrom fastapi.openapi.utils import get_openapi\n\n\n\nfrom typing import Optional, Any\nfrom pathlib import Path\nfrom sqlalchemy.orm import Session\n\n\n\nfrom app.schemas.user import User\nfrom app.api.v1.api import api_router\nfrom app.core.config import settings\n\nfrom app.api import deps\nfrom app import crud\n\n\n\napp = FastAPI(title=\"UHC API Quals\", openapi_url=None, docs_url=None, redoc_url=None)\nroot_router = APIRouter(default_response_class=UJSONResponse)\n\n\n\n@app.get(\"/\", status_code=200)\ndef root():\n    \"\"\"\n    Root GET\n    \"\"\"\n    return {\"msg\": \"UHC Api v2.0\"}\n\n\n@app.get(\"/api\", status_code=200)\ndef root():\n    \"\"\"\n    /api endpoints\n    \"\"\"\n    return {\"endpoints\":\"/v1\"}\n\n\n@app.get(\"/api/v1\", status_code=200)\ndef root():\n    \"\"\"\n    /api/v1 endpoints\n    \"\"\"\n    return {\"endpoints\":[\"/user\",\"/admin\"]}\n\n\n\n@app.get(\"/docs\")\nasync def get_documentation(\n    current_user: User = Depends(deps.parse_token)\n    ):\n    return get_swagger_ui_html(openapi_url=\"/openapi.json\", title=\"docs\")\n\n@app.get(\"/openapi.json\")\nasync def openapi(\n    current_user: User = Depends(deps.parse_token)\n):\n    return get_openapi(title = \"FastAPI\", version=\"0.1.0\", routes=app.routes)\n\napp.include_router(api_router, prefix=settings.API_V1_STR)\napp.include_router(root_router)\n\n\ndef start():\n    import uvicorn\n\n    uvicorn.run(app, host=\"0.0.0.0\", port=80, log_level=\"debug\")\n\nif __name__ == \"__main__\":\n    # Use this for debugging purposes only\n    import uvicorn\n\n    uvicorn.run(app, host=\"0.0.0.0\", port=80, log_level=\"debug\")\n
```
- We can not write file because it is asking for a debug key (just like the firdt backend)
- This line in the main.py app could help us `app.core.config import settings`
- Let's try to see `/home/htb/app/core/config.py`  
![image](https://user-images.githubusercontent.com/96747355/168454341-3a9307ef-b9b6-40c0-a7cd-b3fabebcd9a8.png)
- we apply changes and fetch it  
![image](https://user-images.githubusercontent.com/96747355/168454355-48636377-5d66-4967-aaa4-84226af2d8d0.png)
- And we see in the settings that it gets it's secret from an env var `JWT_SECRET: str = os.environ['API_KEY']`
https://0xdf.gitlab.io/2022/05/02/htb-backendtwo.html