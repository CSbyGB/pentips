# Make slides with Marp

## What is it?

- Marp is a tool to make presentations using markdown

## How to

- At the begining of the file you need to specify that it is a marp slideck
```
---
marp: true
title: Presentation title
author: Author Name
description: presentation description
size: 16:9
header: 'Put a header here'
footer: 'Put a footer here'
paginate: true
_paginate: false        # Remove page number of page 1
---
```
- You can use .svg images or any other format amd insert them as usual in markdown `![](image.link)` to redmension and image you can add `width:400px` between the "[]"
- You can change the style (you can use color code instead of color name just like in html
```
<style>
  :root {
    --color-background: black; 
    --color-foreground: green;
  }
  h1 {
    font-family: Courier New;
  }
</style>
```
- Presentation tips

```
- Bullet point
  - Child bullet
- **Bold**
- _Italic_
- `code`
<!--
Add a comment this way
-->
```

- You can use [draw.io](https://draw.io) to make your graph and visuals

## Tools

- VSCodium or Visual studio code
  - The Marp add-on for vscodium (or visual studio code) this will show you the visual of your md.

## Resources

- [Marp](https://marp.app/)
- [VS Codium - VSCodium is a community-driven, freely-licensed binary distribution of Microsoft’s editor VS Code.](https://vscodium.com/)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Directives](https://github.com/marp-team/marp/blob/main/website/docs/guide/directives.md) (very useful to see how to set up yourself if you start from scratch)