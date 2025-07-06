# PortSwigger Web Security Academy -  Exploiting LLM APIs with excessive agency

## Instructions

```txt
To solve the lab, use the LLM to delete the user carlos.
```

## What is excessive agency

Excessive Agency in an LLM (Large Language Model) refers to a security vulnerability where the LLM performs actions that go beyond its intended scope or permissions.

Imagine you hire a personal assistant whose job is to read your emails and summarize them for you.

- Appropriate Agency: The assistant reads your emails and gives you a concise summary. That's it.

- Excessive Agency: You ask the assistant to summarize an email, but because they also have access to your email sending function (even if they weren't supposed to use it for this task), they decide to send a new email to someone else based on something in the summary, without your explicit instruction or permission. Or, perhaps they have access to your bank account viewing tool, and suddenly decide to transfer money because they misinterpret a request.

In the context of LLMs, this often happens when:

- Excessive Functionality: The LLM is given access to tools or plugins that have more capabilities than strictly necessary for its purpose (e.g., a "read email" tool also has "send email" functionality).

- Excessive Permissions: The LLM's underlying tools connect to systems (like a database) with more privileges than needed (e.g., a tool designed to read data has write or delete permissions).

- Excessive Autonomy: The LLM is allowed to make high-impact decisions or take actions without sufficient human oversight or confirmation (e.g., deleting data without asking for user approval).

It's essentially the LLM being able to "do too much" with the access it has, leading to unintended and potentially harmful consequences like data breaches, system modifications, or unauthorized actions.

## Resolution

The chat below, is basically what it took to solve the lab:  

![Delete Carlos](../.res/2025-07-06-13-32-23.png)  

See here how it gives Carlos' password as well so easily

Lab is solved! :D

![Solved](../.res/2025-07-06-13-32-59.png)