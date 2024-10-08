# Vulnmachines - Web - Basic Labs

## Cross Site Scripting

### XSS Lab 1

We end up here, our user agent is reflected to us:  

![user agent](../.res/2023-04-30-14-13-51.png)  

We put on the intercept on burp (we can also work in the repeater tab).  
Let's try this first `<script>alert(1)</script>`

![Burp](../.res/2023-04-30-14-16-07.png)  

It works  

![Burp request](../.res/2023-04-30-14-22-33.png)  

![Pop up](../.res/2023-04-30-14-15-29.png)  

### XSS Lab 2

We end up here  

![cookie lab](../.res/2023-04-30-14-17-40.png)  

This time our cookie is reflected to us  

![Burp cookie reflected](../.res/2023-04-30-14-18-35.png)  

Let's try the same paylaod `<script>alert(1)</script>`

![Burp request](../.res/2023-04-30-14-20-14.png)  

It works again  

![Burp](../.res/2023-04-30-14-21-31.png)  

![alert](../.res/2023-04-30-14-21-00.png)  

### XSS Lab 3

We land here  

![land page](../.res/2023-04-30-14-25-36.png)  

Here is what it looks like in burp  

![Burp](../.res/2023-04-30-14-28-16.png)

These 2 snippets of js are interesting  

```javascript
[STRIPPED]
<script>
if (localStorage.getItem("alert-labs") === null) {
	 localStorage.setItem("alert-labs", "alert-labs");
}
</script>
[STRIPPED]
<script>
if (localStorage.getItem("alert-labs") !== null) {
		 document.getElementById("content").innerHTML = "<span style=\"font-size:0.4em;\"> LocalStorage will help You: </span><br>" + localStorage.getItem("alert-labs") + "";
}
</script>
[STRIPPED]
```

The first snippet is going to set the local storage to `alert-labs` if null.  
The second one is going fetch the localStorage and print it in the page.  
This time we need to modify this in our browser.  
In firefox we can modify the localStorage property and enter this `<img src=1 onerror="javascript:alert(1)"></img>`. Just like this  

![Firefox LocalStorage modification](../.res/2023-04-30-15-27-18.png)  

This way if we refresh the page we get our alert:  

![LocalStorage pop up](../.res/2023-04-30-15-30-38.png)

Note that in a real context, this would require a user interaction.

The following articles are really interesting to see how a vulnerability like this could be exploited in a real context: 

- [DOM-based HTML5-storage manipulation](https://portswigger.net/web-security/dom-based/html5-storage-manipulation)
- [DOM-based vulnerabilities - Portswigger](https://portswigger.net/web-security/dom-based)
- [Wagtail XSS + LocalStorage = Account Hijack on TechAnarchy](https://www.techanarchy.net/wagtail-xss-localstorage-privesc/)
- [LocalStorage exploit via Cross Site Scripting on Appsec Ramblings](http://appsecramblings.blogspot.com/p/blog-page_8462.html)
- [XSS - The LocalStorage Robbery by Jerry Shah](https://shahjerry33.medium.com/xss-the-localstorage-robbery-d5fbf353c6b0)

- [This article](https://portswigger.net/research/web-storage-the-lesser-evil-for-session-tokens) by James Kettle is really interesting and worth reading to have a better understanding of security of Web Storage

## IDOR

We end up here so let's try and create a pdf file.  
We get an id let's take it and paste it to get our generated pdf  

![PDF creation](../.res/2023-05-01-17-38-43.png)

Our request to get our pdf looks like this:  

![Request](../.res/2023-05-01-17-40-37.png)  

Let's send this to the repeater and try to get other pdf_id
It seems like we can enumerate any pdf on the platform  

![PDF enum](../.res/2023-05-01-17-43-38.png)  

Let's do this with the intruder to find the one with our flag  

We put our var on the id like this  

![Variable](../.res/2023-05-01-17-47-02.png)  

Here is how to set the payload  

![Payloads](../.res/2023-05-01-17-47-53.png)  

We can launch the attack. The length of the response will be very helpful to know which file is the one we want. There is one that is considerably different than the other  

![Length](../.res/2023-05-01-17-49-39.png)  

Let's try it. If we show the response in the browser we find the flag in our PDF!  

![Flag](../.res/2023-05-01-17-50-53.png)

## Local File Inclusion

We end up here:  

![landing](../.res/2023-05-03-15-52-51.png)  

Let's try to click on search  

![Search](../.res/2023-05-03-15-56-24.png)  

Here is what we see on burp  

![Request](../.res/2023-05-03-15-57-02.png)  

Let's send this to repeater and try to access to file from the server.  
It works we can access `/etc/passwd`  

![LFI](../.res/2023-05-03-15-58-16.png)  

Apparently we do not even need to move in the folders, sending a request with just `/etc/passwd` works as well.
