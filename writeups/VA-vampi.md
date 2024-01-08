# Vulnerable APIs - Vampi

## General info, Get it and deploy it

- [Official repo](https://github.com/erev0s/VAmPI)

### Infos

#### List of Vulnerabilities

> This is the list from the documentation  
> To keep track, I will check everytime I find one

- [x] SQLi Injection
- [x] Unauthorized Password Change
- [x] Broken Object Level Authorization
- [x] Mass Assignment
- [x] Excessive Data Exposure through debug endpoint
- [x] User and Password Enumeration
- [x] RegexDOS (Denial of Service)
- [x] Lack of Resources & Rate Limiting

### Deploy it

To deploy it I used Docker

- `docker run -p 5000:5000 erev0s/vampi:latest`  

![Deployed](../.res/2024-01-08-07-47-09.png)  

#### Use Swagger editor

- Go [here](https://editor.swagger.io/)
- Get the yaml in your local machine from [here](https://github.com/erev0s/VAmPI/blob/master/openapi_specs/openapi3.yml) or `wget https://raw.githubusercontent.com/erev0s/VAmPI/master/openapi_specs/openapi3.yml`
- Then you can play in the browser with the request and you get a full list of requests  
![swagger editor](../.res/2024-01-08-08-00-16.png)  

## Hack it

- First thing we would usually do is to enumerate a little, however, we do have the doc here so it seems to be enough for now. We can dive in.
- Launch burp or zap (I will be using burp)
- I am going to look around a little and then try to exploit the vulnerabilities mentionned in the list from the doc (that I pasted above)

If we access http://127.0.0.1:5000/ we get this page  

![Home page](../.res/2024-01-08-07-51-04.png)  

We already have some data but let's still try the request to create the db just to see what happens.

![createdb](../.res/2024-01-08-08-51-49.png)  

### Create a user

We can do this with a post request on the endpoint `/users/v1/register`

![Creat a user](../.res/2024-01-08-09-21-11.png)

### Vulnerability: Create an admin user = Mass assignment

We are not suppose to be able to create an admin user but if we use the previous endpoint but add in the json the parameter `"admin": true,` we are able to create our admin user.

![Create admin user](../.res/2024-01-08-09-24-31.png)  

We can see that our user is indeed created.

![Admin user created](../.res/2024-01-08-10-10-24.png)

### Login

Once we created a user we can use it to login by issuing a post request on this endpoint `/users/v1/login`. This way, we will get a token.  

![login](../.res/2024-01-08-10-29-19.png)  

Then when we will need to do something with a logged in user we will just have to specify the token we just got like this in our request header:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MDQ3Mjc3NDgsImlhdCI6MTcwNDcyNzY4OCwic3ViIjoiY3NieWdiIn0.OyXjErURxhzD62lZpmRaHmvONniGZyjq6KlTQoq6fqs
```

### Vulnerability: Excessive Data Exposure through debug endpoint

We have a request to list the users and we have an even more interesting request `/users/v1/_debug` that gives also the users passwords.  
So this is definitely excessive data exposure  

![users debug](../.res/2024-01-08-08-58-31.png)  

### User enumeration

When trying to login with a non existent user we have the response "Username does not exist".  

![user does not exist](../.res/2024-01-08-09-29-46.png)

Another way to enumerate user is to try to find a specific user using the endpoint `/users/v1/{username}` with a get request.

- When a user exists we get this  

![user exists](../.res/2024-01-08-10-43-56.png)  

- When it does not we get this:  

![user does not exist](../.res/2024-01-08-10-44-46.png)  

### Password enumeration

We have a way of knowing if a password is correct or not. This vulnerability is really helpful for bruteforce attacks.  

If we try to login with an existing user but with a wrong password, we get a specific message "Password is not correct for the given username.".  

![password enumeration](../.res/2024-01-08-09-33-34.png)  

### Unauthorized Password Change

When issuing a PUT request on this endpoint, we can change our password `/users/v1/<username>/password`.  
But can we change another user password.  
Let's try to change the password of `admin` user with my token of the csbygb non admin user.  
It works!

- Request to change the password of admin with csbygb user's token

![change password of another user](../.res/2024-01-08-10-37-49.png)

- The password of admin has changed

![admin user's password changed](../.res/2024-01-08-10-39-00.png)

### SQL Injection

If we add a quote in the request to get the users we get a sql error  

![SQL error](../.res/2024-01-08-10-53-40.png)

```sql
sqlalchemy.exc.OperationalError: (sqlite3.OperationalError) unrecognized token: "'John.Doe''"
[SQL: SELECT * FROM users WHERE username = 'John.Doe'']
(Background on this error at: https://sqlalche.me/e/20/e3q8)
```

- So here we know now that the database is sqlite
At this point we can continue with sqlmap and this will dump the db `sqlmap -u http://localhost:5000/users/v1/csbygb* --dbms=sqlite --dump`

![Dumped database](../.res/2024-01-08-11-57-00.png)

### Vulnerability: BOLA

We can find books that do not belong to us. Using the book title as the ID with this endpoint `/books/v1/{book-title}`  

So here with the token of my user csbygb I am able to access a book that belongs to the user name1.  

![bola](../.res/2024-01-08-13-21-10.png)

### Lack of Resources & Rate Limiting

Here we are able to send a lot of request without being blocked in anyway by the application.  
For example we can send plenty of requests to bruteforce the login.  
I am going to use burp (pro version) intruder for this. I send the login request to the intruder, I set up variables on username and password.  

![set variables](../.res/2024-01-08-13-28-31.png)  

So here we were able to send more than 800 request without being blocked  

![no rate limiting](../.res/2024-01-08-13-40-19.png)  

### ReDos

> For the reference of this vulnerability on OWASP, you can find it [here](https://owasp.org/www-community/attacks/Regular_expression_Denial_of_Service_-_ReDoS)

So we could try one of the regex provided in the OWASP document.  
We could try this on this endpoint `/users/v1/{username}/email` if we issue a put request here we are suppose to be able to update our email address.  
We need to be logged in for this.  
Using this request we were able to trigger the vulnerability and dos the application.  

![Denial of service](../.res/2024-01-08-14-32-08.png)  

> Avoid to do this in real context except if it is a request or if you have the authorization of you customer. With these types of attack you are actually impacting the availability of the application.

## Resources

### ReDos

- [Regular expression Denial of Service - ReDoS - Hacktricks](https://book.hacktricks.xyz/pentesting-web/regular-expression-denial-of-service-redos)
- [The Regular Expression Denial of Service (ReDoS) cheat-sheet - James Davis](https://levelup.gitconnected.com/the-regular-expression-denial-of-service-redos-cheat-sheet-a78d0ed7d865)