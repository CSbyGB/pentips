# TryHackMe - OWASP Top 10 2021

![OWASP Room](../.res/2023-07-08-13-22-41.png)  

- [Room on THM](https://tryhackme.com/room/owasptop102021)

## IDOR

If we access to note 0 we get the flag  

## Cryptographic failures (Task 8)

Here I did not find the note the developer left, however I always like to have a look at the images path.
So i just right click on the image and "open image in new tab"  
We get this path `/assets/images/lake-taupo.jpg`  
If we go one directoy up, we can see that we have directory listing.  
If we go to the assets directory we can see a db file  

![db file](../.res/2023-07-08-13-15-27.png)  

We can save it and check it.  

We get interesting data  

![sqlite](../.res/2023-07-08-13-20-49.png)  

Let's check the hashes on crackstation  

- Here is the admin hash decoded  

![admin](../.res/2023-07-08-13-22-12.png)  

- Bob's password  

![Bob](../.res/2023-07-08-13-23-57.png)  

Crackstation does not crack Alice's password.  

We can now login as admin (turns out the note of the dev was on the login page😅)  

We get the flag.  

## Command Injection (task 10)

First let's make an `ls` for this we need to enter this in the form `$(ls)`  

![ls](../.res/2023-07-08-13-41-16.png)  

To answer the second question we need to `cat /etc/passwd`  
For the next question `whoami`  
The answer to the 4th question is in /etc/passwd  
For the last question we need to `cat /etc/alpine-release`  

## Insecure design (Task 11)

For this I used the security question about the favorite color and tried different color until it worked.  

![pass reset](../.res/2023-07-08-13-57-11.png)  

So now we can use the password and log in as joseph.  

Then we just need to check in joseph's private folder to get the flag.  

## Security Misconfiguration (task 12)

To read the content of the app.py we need to type `import os; print(os.popen("cat app.py").read())`  

## Vulnerable and Outdated Components (Task 15)

With a simple search we find an authenticated RCE on CSE Bookstore. We can get the exploit [here](https://www.exploit-db.com/exploits/47887)  
We get a shell  
![rce](../.res/2023-07-08-14-16-02.png)  

We just need to cat the flag now.

## Identification and Authentication Failures (Task 17)

Here we just need to register ` darren` and ` arthur` and it works.  

## Software Integrity Failures

Here we just need to do this  

![hash](../.res/2023-07-08-14-27-36.png)

## Data Integrity Failures

Once logged in we get our jwt token  
![JWT](../.res/2023-07-08-14-31-53.png)  

`eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6Imd1ZXN0IiwiZXhwIjoxNjg4ODQzNzIyfQ.Fm8_HaBydwrHZfsTKwFAnWFXicPHbyivUXfojVDF8pw`  

- We change algo to none
`eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0=`

- We make ourselves admin
`eyJ1c2VybmFtZSI6ImFkbWluIiwiZXhwIjoxNjg4ODQzNzIyfQ==`

- Here is the new token

`eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJ1c2VybmFtZSI6ImFkbWluIiwiZXhwIjoxNjg4ODQzNzIyfQ.`  

This way we get the flag  

## Security Logging and Monitoring Failures

From the provided txt file we can easily conclude that the attacker has ip 49.99.13.16 and is carrying out a brute force attack.  

## SSRF (Task 22)

If we go to the admin area we can see this  

![localhost](../.res/2023-07-08-14-43-31.png)  

When we Dowload the resume it makes this request  

![Resume](../.res/2023-07-08-14-44-59.png)  

This means we could try to send a request to our own server.  
Let's see if it works. It does our server gets requested, as we can see  

![Request](../.res/2023-07-08-14-48-04.png)  

To get the api we need to intercept with netcat just like in they showed in the example so we just need to `nc -lvp 80`  
And we get the flag

### Access admin area

My first error was to try this in the repeater.  
![repeater](../.res/2023-07-08-15-09-54.png)
But when I copied the url from the request in the repeater I actually got the flag  
`http://<IP>:8087/download?server=localhost:8087/admin%23&id=1`  
