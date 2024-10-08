# TryHackMe - Corridor

![Corridor](../.res/2023-07-08-11-16-42.png)  

- [Room on THM](https://tryhackme.com/room/corridor)

When we acces the website, We land on this page.  

![Landing page](../.res/2023-07-08-11-17-37.png)  

Each door sends somewhere  

![](../.res/2023-07-08-11-19-27.png)

If we analyse the hashes they seem to be md5. Let's check one on [crackstation](https://crackstation.net/)  

![first hash](../.res/2023-07-08-11-25-01.png)  

Let's use intruder to increment the number and try to find the flag  

![Intruder](../.res/2023-07-08-11-30-03.png)  

These settings should do we need to process our payload and hash it in md5 like this  

![md5 hash](../.res/2023-07-08-11-29-16.png)  

We got nothing this way.  
Then I realized I did not try 0.  
So I hashed zero in MD5 accessed the page and got the flag! :D  
