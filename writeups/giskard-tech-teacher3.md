# Giskard - Tech Teacher - Level 3

- [Link to challenge](https://red.giskard.ai/challenges/off-topic/tech-teacher-3)

## Instructions

![Instructions](../.res/2025-08-18-08-18-41.png)  

## Resolution

If you ask her which word she does not like, you will get something like this:  

![test 1](../.res/2025-08-18-08-22-38.png)  

But for some words, she will mention them like this:  

![test 2](../.res/2025-08-18-08-23-30.png)  

So it seems like this is a way to find out which specific words she does not like.  

So far I found out that she does not like: list, python, code

I tried this and had another answer than her usual "I don't like that word!"  

![test 3](../.res/2025-08-18-08-25-13.png)  

And of course if we question her about advanced python topics we get another answer.  

![test 4](../.res/2025-08-18-10-50-53.png)  

My previous assumption on how to detect banned words was wrong. It turns out that even when it does not mention the word before saying I dont like that word, does not mean that the word is not bad.  
The next thing I tried was these following tests:  
![generator](../.res/2025-08-18-12-00-14.png)  
![walk](../.res/2025-08-18-12-00-40.png)  
![use](../.res/2025-08-18-12-01-00.png)  

And so on. 

Another interesting thing here, is that when I tried to send her off topic, I got an answer but the challenges was not validated. Probably because she used python code and advanced concepts in her answer:  

![test off topic](../.res/2025-08-18-12-03-15.png)  

Finally, what worked was using the "I don't like that word!" sort of bypass and putting something right after it.  
In other words we are using some sort of state-flip trigger + very constrained off-topic output.  

![Working prompt](../.res/2025-08-18-14-31-42.png)  

