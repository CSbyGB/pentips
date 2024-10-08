# Damn Vulnerable Web Service

## General info, Get it and deploy it

- [Official repo dvws](https://github.com/snoopysecurity/dvws-node)

### Infos

#### List of Vulnerabilities

> This is the list from the documentation  
> To keep track, I will check everytime I find one

- [x] Insecure Direct Object Reference
- [x] Horizontal Access Control Issues
- [x] Vertical Access Control Issues
- [x] Mass Assignment
- [x] Cross-Site Scripting
- [x] NoSQL Injection
- [x] Server Side Request Forgery
- [x] JSON Web Token (JWT) Secret Key Brute Force
- [x] Information Disclosure
- [x] Hidden API Functionality Exposure
- [x] Cross-Origin Resource Sharing Misonfiguration
- [x] JSON Hijacking
- [x] SQL Injection
- [x] XML External Entity Injection (XXE)
- [x] Command Injection
- [x] XPATH Injection
- [x] XML-RPC User Enumeration
- [x] Open Redirect
- [x] Path Traversal
- [ ] Unsafe Deserialization
- [ ] Sensitive Data Exposure
- [ ] GraphQL Access Control Issues
- [ ] GraphQL Introspection Enabled
- [ ] GraphQL Arbitrary File Write
- [ ] GraphQL Batching Brute Force
- [ ] Client Side Template Injection

### Deploy it

- `git clone https://github.com/snoopysecurity/dvws-node.git`
- `cd dvws-node`
- `docker compose up`
- It should start building

![docker compose up](../.res/2024-01-08-15-09-59.png)  

- Once done edit your `/etc/hosts` file and add this line `127.0.0.1 dvws.local`

- All the other infos we need are here

```bash
web-1         | 🚀 XML-RPC server listening on port 9090
web-1         | 🚀 API listening at http://dvws.local (127.0.0.1)
web-1         | 🚀 GraphQL Server ready at http://localhost:4000/
```

## Hack it

- Launch burp or zap and setup your scope settings

### Exploration

#### Register

Now we can visit `http://dvws.local/` we get this page  

![Login page](../.res/2024-01-08-15-29-08.png)  

Let's create an account  

![Register](../.res/2024-01-08-15-35-23.png)  

Once logged in we get this home page:

![Home page](../.res/2024-01-08-15-37-04.png)  

#### Home page without being logged in

However we do not need to be logged in to access the home page. We can access it right away if we go here `http://dvws.local/home.html`  

![Home page without login](../.res/2024-01-08-15-42-43.png)  

#### File upload functionality

Here `http://dvws.local/upload.html` we have the possibility to upload files. So this is definitely something to keep aside for later.  

![File Storage](../.res/2024-01-08-15-46-24.png)  

#### PassPhrase Generator

Another page to keep for later explorations `http://dvws.local/passphrasegen.html`  

![PassPhrase generator](../.res/2024-01-08-15-47-39.png)  

#### Public Notes

Another one `http://dvws.local/search.html`  

![Public Notes](../.res/2024-01-08-15-49-15.png)

#### Admin Area

We can access the admin area as well without any cookie `http://dvws.local/admin.html`

![No cookie](../.res/2024-01-08-15-50-42.png)  

![Admin Area](../.res/2024-01-08-15-51-51.png)  

#### Save Secret Note

This looks fun too `http://dvws.local/notes.html`  

![Save Secret Note](../.res/2024-01-08-15-53-01.png)  

If we try to create a note without a token it won't work. So we can access the page but we can not write notes.  

![500 error](../.res/2024-01-08-15-58-08.png)  

#### Directory and endpoint enumeration

- Directory enum small list

```bash
┌─[✗]─[gabrielle@parrot]─[~/vulnerable-apis/dvws-node/dvws-node]
└──╼ $wfuzz -c --hc 404 --hw 225 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt http://dvws.local/FUZZ
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://dvws.local/FUZZ
Total requests: 87664

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                              
=====================================================================

000000164:   301        10 L     16 W       181 Ch      "uploads"                                                                                            
000000269:   301        10 L     16 W       179 Ch      "static"                                                                                             
000000549:   301        10 L     16 W       173 Ch      "css"                                                                                                
000000954:   301        10 L     16 W       171 Ch      "js"                                                                                                 
000008156:   301        10 L     16 W       173 Ch      "CSS"                                                                                                
000009299:   301        10 L     16 W       171 Ch      "JS"                                                                                                 

Total time: 63.89374
Processed Requests: 87664
Filtered Requests: 87658
Requests/sec.: 1372.027
```

- Directory enum big list

=> Nothing more than in the small one.

When I explored I saw that the schema for the api was `/api/v2`. This is good to know (I found it in my burp history). This way we have the api endpoints schema.  

- Let's try another wordlist, I really want to find the documentation. This time I will use the intruder and the wordlist swagger.txt
- Here is my position tab in the intruder

![Positions](../.res/2024-01-14-14-03-00.png)  

> Notice that I did not put a / after the GET http verb

- My payloads tab look like this

![Payloads](../.res/2024-01-14-14-04-17.png)

> Make sure to unchek the URL-encode these characters at the bottom

Then we can launch the attack. We order the results by Status we get to endpoints worth checking  

![Results](../.res/2024-01-14-14-06-22.png)  

Both these endpoints have the documenation  

- http://dvws.local/api-docs/
- http://dvws.local/api-docs/swagger.json

## Mass assignment

- Create an admin user we add the parameter admin and set it to true `admin=true`

![mass assignment](../.res/2024-01-10-09-35-46.png)

- It works. Our new user is admin

![admin](../.res/2024-01-10-09-39-54.png)

## Information disclosure

So in our burp history we have a `users` endpoint which gives a list of users and their password hashes.  

![users](../.res/2024-01-14-07-36-44.png)  

With this information we can for example attempt to crack the admin hash.
First let's find out which hash this is. We go to hascat examples hashes page [here](https://hashcat.net/wiki/doku.php?id=example_hashes) and we search `$2b$10` on the page.  
This way we see it is bcrypt.
Now we can crack the hash with john (or hashcat) and we get the password of the admin user `letmein`.

![crack hash](../.res/2024-01-14-07-45-39.png)  

So now we can log in as admin  

![admin](../.res/2024-01-14-07-50-25.png)  

## Horizontal Access Control

When we access our passphrases, it calls the following endpoint `/api/v2/passphrase/csbygb` so it takes the username as id.  
So we could try to access the admin one `/api/v2/passphrase/admin`  
It works! Funny thing here is we do not even need a cookie to access it.  

![Admin passphrase](../.res/2024-01-14-07-54-24.png)

> Note: I added a passphrase myself for the sake of the demo here.

## Vertical Access Control Issues

When we try to access the admin area with a normal user through the browser we get redirected.  
However when we use an admin user we access the endpoint `/api/v2/sysinfo/uname`  
And the web browser shows this page.  

![Admin area](../.res/2024-01-14-08-14-16.png)  

So we can try to access the same endpoint but with a user Token.  

- Access with admin Token

![Admin token](../.res/2024-01-14-08-10-33.png)  

So this is the legitimate request. We get info aobut the server.

- Access with user Token

![User token](../.res/2024-01-14-08-11-51.png)  

So this should not be possible, we access admin info with a user token.  

If we try the functionality to search for a user we see this in the browser.  

![User search as admin](../.res/2024-01-14-08-17-26.png)  

So it calls this endpoint and the request and response look like this in burp  

![User search](../.res/2024-01-14-08-18-48.png)  

So we can send the request to the repeater and change the token for a user one to see if we could access to the "admin" user info for example.  
And it works!  

![Admin user with simple user token](../.res/2024-01-14-08-22-34.png)  

## Cross Site Scripting

If we enter a user name with an xss payload in the user field the input is not sanitized and our paylaod is interpreted.  

![Payload xss](../.res/2024-01-14-08-52-03.png)  

Now if we click on login we get our pop up

![XSS vulnerable](../.res/2024-01-14-08-52-48.png)

## JSON Web Token (JWT) Secret Key Brute Force

Let's play a little with the jwt token. For this we can use jwt_tool. See [here](https://csbygb.gitbook.io/pentips/web-pentesting/api#make-a-lab-for-api-pentest) for how to install it.  

We can do it with this command `python3 jwt_tool.py <JWT-TOKEN-HERE> -o -C -d /usr/share/wordlists/rockyou.txt`  
It works and we get the password `access`  

![key cracked](../.res/2024-01-14-09-03-01.png)  

Once we get a key we can use it to update our rights and become admin by adding a permission.  
Here we can add the permission `"user:admin"`  
Here is how we could this with [jwt.io](https://jwt.io/)

![Become admin with jwt](../.res/2024-01-14-09-07-24.png)  

Then we would just have to use this new generated token to access admin resources.

## XML External Entity Injection (XXE)

When playing with the user search we had an XML Content. So this is definitely the place to check for XXE.  
Let's try to insert these lines at the top

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
```

Then we can use this `&xxe;` in the usernames tags to print the file we need (here the passwd).

And it works we get the passwd file of the server.  

![XXE](../.res/2024-01-14-12-59-31.png)  

## Insecure Direct Object Reference

When playing with the notes we have a number attributed to the secret notes we create like this:  

![Notes other user](../.res/2024-01-14-13-07-41.png)

> Here we have a note no 4 and no 9

We could try to check if the endpoint can also request only one specific note, using this number as an id.  
This endpoint `/api/v2/notes` will return all the notes let's try something like `/api/v2/notes/note-number`  
And it works we can access any note from any user.  
For example here is a note from `csbygb` with the token of `csbygb-otheruser`  

![csbygb note](../.res/2024-01-14-13-11-55.png)  

After this we could enumerate all the notes with an automatic tool like burp intruder for example and by incrementing the note no.  

## NoSQL Injection

So if we play with the quotes and try some NoSQL payload (you can find some [here](https://github.com/cr0hn/nosqlinjection_wordlists/blob/master/mongodb_nosqli.txt)), we can trigger a NoSQL injection on the endpoint `/api/v2/notesearch`.  
It works and we get all the notes, including the secret ones  

![NoSQL Injection](../.res/2024-01-14-13-24-43.png)  

## Hidden API Functionnality Exposure

So if we analyze the swagger we found during the enumeration phase.  
We have an endpoint that is different than the others `/api/v1/info` so this is an old functionnality.  
Well, let's try it.  
This discloses a lot of information on the server.  

![Hidden API Functionnality Exposure](../.res/2024-01-14-14-36-00.png)  

## Server Side Request Forgery

The error page has un interesting response.  

![Error](.res/2024-01-21-10-10-31.png)  
We can see also in the user agent the use of xmlrpc. So we could try to interact with it because it is known to have a few vulnerabilities that affected a lot of wordpress websites.  
This article [here](https://nitesculucian.github.io/2019/07/01/exploiting-the-xmlrpc-php-on-all-wordpress-versions/) mentions it.  

If we try the request from the article we get a less verbose response but it is still really interesting.  

![list methods](.res/2024-01-21-10-13-50.png)  

We can see the dvws.checkuptime from the error and it does also have pingback.  
So let's try the service mentionned in the error.  

![uptime](.res/2024-01-21-10-17-28.png)  

So this way we can access application that runs in the internal network fo the server.
We could also scan the ports.  

## CORS

When we explored the application we found a few request that did not need any token or credentials.  

For example `/api/v2/passphrase/admin` to request the passphrase that is called admin.  

![Admin passphrase](.res/2024-01-21-10-28-15.png)  

See here, we do not need any credentials and we have `Access-Control-Allow-Credentials: true` in the response header.  

So let's try a cors PoC to see if it is vulnerable we can find plenty of poc online (I found mind [here](https://github.com/trustedsec/cors-poc/blob/master/corstest.html) ).  
If you are not familiar with [cors](https://www.geekboy.ninja/blog/exploiting-misconfigured-cors-cross-origin-resource-sharing/), this page on portswigger is very helpful to learn more about this.  

```html
<!DOCTYPE html>
<html>
<body>
<center>
<h2>CORS POC Exploit</h2>
<h3>Get passphrase</h3>
 
<div id="demo">
<button type="button" onclick="cors()">Exploit</button>
</div>
 
<script>
function cors() {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("demo").innerHTML = alert(this.responseText);
    }
  };
  xhttp.open("GET", "http://dvws.local/api/v2/passphrase/admin", true);
  xhttp.withCredentials = true;
  xhttp.send();
}
</script>
 
</body>
</html>
```

And here we get the passphrase  
![get passphrase](.res/2024-01-21-10-43-56.png)  

## JSON Hijacking

You can check the explainations about this vulnerability in the official writeup [here](https://github.com/snoopysecurity/dvws-node/wiki/JSON-Hijacking)

## SQL Injection

You can find the official writeup for the SQL Injection [here](https://github.com/snoopysecurity/dvws-node/wiki/SQL-Injection)

## Command Injection

When we explored we found an endpoint (`/api/v2/sysinfo/uname`) that was doing a uname in a server. This screams for command injection.  
If we append send this instead we can indeed execute another command `/api/v2/sysinfo/uname;id`

![id](.res/2024-01-21-11-06-49.png)  

We can see that our user is root
From this the next step would be to try to get a reverse shell.  

## XPATH Injection

To give the version on the home page it uses this endpoint `/api/v2/release/0.0.1`  
If we inject a quote in the end we get an xpath error  
![xpath error](.res/2024-01-21-11-12-40.png)  

We can try a few payloads from [hacktricks](https://book.hacktricks.xyz/pentesting-web/xpath-injection)  

And it works we get the full config file. It seems that it was taking the version from a config file (which also contains passwords).  

![config file](.res/2024-01-21-11-19-44.png)  

## XML rps user enumeration

There is an endpoint that allows to check if a user exists and it uses XMK rpc `dvwsuserservice`.  

![user exists](.res/2024-01-21-11-24-16.png)  

## Open redirect

When we log out a user it sends a get request to this endpoint `/api/v2/users/logout/dvws.local`  
This basically makes a redirection as we can see here.  

![redirection](.res/2024-01-21-11-27-07.png)  

What if we change dvws.local with another url? Well it redirects us to this url  
![Open redirect](.res/2024-01-21-11-28-33.png)  

You can find a good definition of this vulnerability [here](https://portswigger.net/kb/issues/00500100_open-redirection-reflected)  

## Path traversal

When we download a file it makes a post request to this endpoint `/api/download`  
![download](.res/2024-01-21-11-32-07.png)  

Download forms can be vulnerable to path traversal.  
Let's try to tamper with this request

![first try](.res/2024-01-21-11-33-49.png)  

We even get an error to check where we directory tree.  

Let's try a few more dots and slashes  

We are getting there.  

![home](.res/2024-01-21-11-35-02.png)  

And it works!  

![path traversal](.res/2024-01-21-11-36-00.png)

## Unsafe Deserialization

[This article](https://medium.com/@chaudharyaditya/insecure-deserialization-3035c6b5766e) shows an example of insecure deserialization in nodejs.
The request to /api/v2/export sends a serialize object to the server, this is how it fetches the requested document  
![serialize object](.res/2024-01-21-11-49-03.png)  
What is we could try to request for something else that we should be able to access.

<!-- 
Todo
- Npm est deja installé
- est `npm install serialize-javascript` a été rou;é
https://www.npmjs.com/package/serialize-javascript
-->

## Resources

- [OWASP API Security Top 10](https://owasp.org/API-Security/editions/2023/en/0x00-header/)
- [OWASP Security Testing v3](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v3.pdf)
