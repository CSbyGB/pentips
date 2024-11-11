# Damn Vulnerable RESTaurant

![Damn Vulnerable restaurant](../.res/2024-11-10-17-19-57.png)  
- [Official repo](https://github.com/theowni/Damn-Vulnerable-RESTaurant-API-Game?tab=readme-ov-file)

## Deploy and launch it

You need to install docker first, here is the process for ubuntu:
```bash
# Install requirements
sudo apt install -y ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
- Get docker desktop deb package from (here)[https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-linux-amd64]

```bash
# Install the deb package
sudo apt install ./docker-desktop-amd64.deb
# Install Damn Vulnerable Restaurant
git clone https://github.com/theowni/Damn-Vulnerable-RESTaurant-API-Game.git
cd Damn-Vulnerable-RESTaurant-API-Game
# Launch it in ethical hacker mode
./start_app.sh
```

## Good to know

The API service will be exposed at http://localhost:8080 by default. API documentation can be found at the following endpoints:

- Swagger - http://localhost:8080/docs
- Redoc - http://localhost:8080/redoc

To close the restaurant at the end of the hacking day, just run:  
`./stop_app.sh`  
Data will persist between stops and starts.

## Hack it

If we go to the docs, we can find an endpoint to register a new user.  
![Register user](../.res/2024-11-11-05-51-17.png)  
Let's do this first.  
We can intercept the request via burp to modify the infos (we can also use swaggerui).  
![Register](../.res/2024-11-11-06-00-21.png)  

So now we can try to login with the post request get token, this way we get a jwt token  
![Login with token](../.res/2024-11-11-07-37-49.png)  
- Here is our token:

```bash
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJnYWJyaWVsbGUiLCJleHAiOjE3MzEzMzA0MjJ9.K8nCkRo3kK341PyWz-zmj4IYIzfgdl2q2KkDffcuiG0
```

We can now add our token in a request. Let's try this in the /profile endpoint

```http
GET /profile HTTP/1.1
Host: localhost:8080
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:129.0) Gecko/20100101 Firefox/129.0
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJnYWJyaWVsbGUiLCJleHAiOjE3MzEzMzA0MjJ9.K8nCkRo3kK341PyWz-zmj4IYIzfgdl2q2KkDffcuiG0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: http://127.0.0.1:8080/
Origin: http://127.0.0.1:8080
Connection: keep-alive
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site:
```

Then we get a response with infos about our user, so we can authenticate properly  

```http
HTTP/1.1 200 OK
date: Mon, 11 Nov 2024 13:27:48 GMT
server: uvicorn
content-length: 109
content-type: application/json
access-control-allow-origin: *
access-control-allow-credentials: true

{"username":"gabrielle","phone_number":"string","first_name":"string","last_name":"string","role":"Customer"}
```

So there's an enpoint that can update a user, let's try to see if we can update our user with it  
![update user](../.res/2024-11-11-08-30-22.png)

- We get a 500 with our token and a non authenticated without token if we try with the role "admin".
- We could try to find a way to get a list of the roles or at least what other roles with have besides customer.  
Let's try out other requests from the swagger to check out what it does and enumerate further.  

### GET /healthcheck 
- works with and without authentication

### GET /menu 
- works with and without authentication

### PUT /menu

- does not works without authentication
- We get a forbidden with a customer token
- Same with PUT /menu/id

### DELETE /menu/id

- Does not seem to work with my token or without a token, the request seems to time out

### POST /orders

- Does not work without token but works with a customer token

### GET /orders

- Works only with token

### GET /orders/id

- Works only with token
> Note: it would be worth checking if I can get another user's order here.

### auth

For the auth categorie of requests we should be good to go for now.  
I will at some point to check what happens with the password reset.

### GET /admin/stats/disk

This one seems interesting. Let's check it out.  
Without a token it returns a response that said we are unauthenticated and with a token we get this:  

```http
HTTP/1.1 403 Forbidden
date: Mon, 11 Nov 2024 16:53:40 GMT
server: uvicorn
content-length: 63
content-type: application/json
access-control-allow-origin: *
access-control-allow-credentials: true

{"detail":"Only Chef is authorized to get current disk stats!"}
```

So here we get another role "chef".  

Let's try to use again the update role endpoint with chef as role and see what we get.  
We still get a 500, and if we try with "Chef" we get this response:  

```http
HTTP/1.1 401 Unauthorized
date: Mon, 11 Nov 2024 17:37:36 GMT
server: uvicorn
content-length: 54
content-type: application/json
access-control-allow-origin: *
access-control-allow-credentials: true

{"detail":"Only Chef is authorized to add Chef role!"}
```

I also wanted to try mass assignment by adding a role in the PUT /profile but did not get me anywhere either.  
So let's explore other things.

## Bola

First let's create another user to see if we can access other users' orders.  

![Register another user](../.res/2024-11-11-11-59-23.png)  

Let's place an order as this user.  
To do this we first need a token, and then we can put our token in the POST /orders request.  
Our order is created with id 2:
![new order](../.res/2024-11-11-12-03-19.png)  

So now let's check to see if we can see order 1 (the order with our former user) with our new user.  
And it works! So here we have a bola also known as idor:  

![bola](../.res/2024-11-11-12-05-57.png)  

## /profile

So now I want to have a look at the /profile endpoint.  
I can modify my own information. How about the other users info.  
![Modify other user](../.res/2024-11-11-15-03-10.png)  
Let's see if the user csbygb has beed modified with the "pwned" strings in the fields.  
![user modified](../.res/2024-11-11-15-04-29.png)  
It did work.  
Before I had tried to add a Chef role on my own user but not on another, but this did not work either.  
What's interesting here is that if we have a valide user our user info get sent back to us but if not, we get a 500.  
So we could try to enumerate users

## User enumeration

With burp community the intruder is too slow.  
Let's try another fuzzer.  

![Coming Soon](../.res/coming-soon.png)

## Resources

- [Web API Security Champion - Krzysztof Pranczk (the creator of the API)](https://devsec-blog.com/tag/web-api-security-champion/)
