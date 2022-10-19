# How to make a gitbook

## Methodology

- Create an account on [gitboook.com](https://www.gitbook.com/)
- Synchronize you account with github using the instructions on [this blog by Thomas Suedbroecker](https://suedbroecker.net/2020/05/14/get-started-with-gitbook-and-github-a-small-guide/) that explains the whole process really clearly :D
- What is great is that if you are not ready to publish it yet it can stay private and you can make it public afterwards. For this you just need to keep the github repo private and your gitbook page connected to it private as well.
- In order to organize the pages as you please you can add this yaml and name it `.gitbook.yaml` and put it in the root of your repo on github.

```yaml
root: ./

structure:  
    readme: README.md  
    summary: SUMMARY.md
```

- So now you can add every new page in your summary to appear. Check out my summary [here](https://github.com/CSbyGB/pentips/blob/main/SUMMARY.md) to get an idea on a structure you can have for your gitbook.
- As you can see on my summary every `## Tile` makes a title and  
  `* [page](networking/basics.md)` makes a page inside it.  
  So this  
  
  ```md
  ## Networking
  * [Basics](networking/basics.md)
  ```
  
  Shows up like this on my gitbook in the side bar  
  ![example](../.res/2022-10-18-09-06-13.png)
- If you want to put embedded resources instead of just clickable text you can use this code snippet 

```txt
{% embed url="URL" %} NAME OF THE RESOURCE {% endembed %}
```

- So for example this  

  ```html
  {% embed url="https://www.trendmicro.com/en_us/what-is/zerologon.html" %} What is ZeroLogon? {% endembed %}
  ```  

  Will look like this on your Gitbook  

![example](../.res/2022-10-18-15-34-51.png)

## Useful extensions on VSCodium

- **Dendron Paste Image** will allow you to copy and paste image in your documents. You can set it up to put the image in the proper directory.
- **Markdown all in One** will provide you what you need to confortably write in markdown, including a preview of what you will get in the page.

## Markdown tips

- To highlight some text we can use `>` it will do something like this  

> I want to highlight text  
> blabla

- To go to the next line we can add 2 spaces at the end of the line.

- To add some code snippets we can use 1 \` `backquote we can also use 3 \`\`\` if we can to make it a block  
`my line of code`
```
My block of code
```
