# Vulnmachines - Web - Intermediate Labs

## SSTI

We end up on this page  

![form](../.res/2023-06-18-10-54-10.png)  

- If we enter `{{` we get an exception

![Exception](../.res/2023-06-18-11-01-11.png)  

- Let's find out which template it uses using James Kettle tree (see more details about this [here](https://csbygb.gitbook.io/pentips/web-pentesting/webvulns/template-injection))  

- This one `{{7*7}}` evaluates

![evaluates](../.res/2023-06-18-11-03-50.png)  

However this `{{7*'7'}}` seems to reveal that some char are filtered  

![filter](../.res/2023-06-18-11-04-57.png)  

If we use double quotes instead it works `{{7*"7"}}`  

![double quotes](../.res/2023-06-18-11-08-12.png)

According to the tree it can be Jinja2 or Twig  

As we have a header leaking the Python version, the template should be Jinja2 as it is based on python  

![python version](../.res/2023-06-18-12-27-04.png)  

- let's try to find the config `{{config}}`

![config](../.res/2023-06-18-12-38-07.png)  

We get the flag here but it seems encoded. If we use burpsuite and decode it as ASCII hex, we get the flag!

![decode](../.res/2023-06-18-12-42-21.png)