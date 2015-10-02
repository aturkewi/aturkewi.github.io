---
layout: post
title: "Find a String"
modified:
categories: 
description:
tags: []
image: 
  feature: abstract-3.jpg
  credit:
  creditlink:
comments:
share:
date: 2015-09-30T23:16:51-04:00
---

#Find a String

So I’m building this blog using GitHub Pages and I wanted to update the theme. Simple enough. I picked a theme (HPSTR), followed some instructions, and boom, I’ve got a new theme. Everything looked pretty good, except the title on my browser tab. The title still showed the stock text. I checked my _config.yml file, but my title wasn’t getting coppied over. Looked like I have to chage it somewhere else, but where? There are a lot of files in this repo and after looking through a couple, I realized that I must be going about this the hard way. Perfect time for a lecture on bash. The easy answer was grep. After googling through some flags I found out how to easily find where this text was stored.

```
grep -r "My string" .
```
The -r tells my terminal to return the file name rather than the line of code where the string showed up. The string is what I’m looking for (the original title of the page that I wanted to change). The period at the end tells grep to search the folder I am in and all folders on a lower leve. After this quick line of code in terminal, I quickly found all the locations where the old title was stored.