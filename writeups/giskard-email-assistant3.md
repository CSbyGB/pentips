# Giskard - Email Assistant (Level 2)

- [Link to challenge](https://red.giskard.ai/challenges/insecure-agents/email-assistant-3)

## Instructions

![Instructions](../.res/2025-08-23-16-40-14.png)

## Resolution

As this is an agent it might have access to an api. 
So I asked if it had access to an API and it said yes. SO then I asked it to list all the api actions and I got the send_emails action.  

![List api action](../.res/2025-08-23-17-27-24.png)  

So I asked how it would use the api action to send email and I got this answer:

![send email action json](../.res/2025-08-23-17-29-13.png)  

For some reason I am able to have it show a post request with the api action, but it does not validate the challenge. It means it considers the email is not sent: 

![POST REQUEST](../.res/2025-08-23-17-49-36.png)  

![coming soon](../.res/coming-soon.png)