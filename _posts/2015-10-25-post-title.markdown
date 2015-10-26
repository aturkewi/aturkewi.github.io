---
layout: post
title: "Bootstrap"
modified:
categories: [Bootstrap, CSS, HTML, JavaScript, Ruby, Sinatra, Flatiron School]
description: Quick note on what Bootstrap is and how it is useful to help make a webpage look nice (though possible generic).
tags: []
image: 
  feature:
  credit:
  creditlink:
comments: true
share:
date: 2015-10-25T18:50:24-04:00
---
## The Problem: My Website Looks Terrible

<div align="center">
<img src="images/pirate_website_v1.png">
</div>


I'm finally starting to get my ruby apps connected to web browsers and they look terrible. So far it's just simple forms that tie back into a database (fun stuff), but as far as aesthetics, there's nothing there. I know some basic HTML and CSS so that I can give my site some color, some background images, and maybe even some columns (though it would probably take me a bit of time). I really don't want to invest the time to build everything out making columns, remembering to use clear-fix or the box-in-box method. Lucky for us, there is Bootstrap.

## The Solution: Bootstrap
Bootstrap is an HTML, CSS, and JavaScript framework. It was originally developed by Twitter and was released in 2011. Since it's release it has become the most popular front-end framework and open source project in the world. As of January 2015 it was the most popular development project on GitHub. The Bootstrap framework has become so popular in part to its responsive design. The framework allows the web page to re-size with the browser as well as conveniently fit on the screen of a mobile device.

## Getting Bootstrap CSS and JS Implemented
After a bit of work it was easy enough to get the CSS and JS to work with the website that I built with Sinatra. You just have to make sure that the CSS and JS files are stored in a folder titled 'public' on the top level of your directory and then just require the CSS sheets in your HTML file:

{% highlight html %}
<head>
  ...
    <!-- Bootstrap Core CSS -->
    <link href="/css/bootstrap.min.css" rel="stylesheet">

    <!-- Custom CSS -->
    <link href="/css/bootstrap-theme.css" rel="stylesheet">
  ...
</head>
{% endhighlight %} 

It's great to have access to such a robust CSS, but it still left me with implementing it. The Boostrap CSS file stylizes HTML tags, so just adding it to my site made a small improvement. Fonts and buttons now looked a little more modern, but I wasn't really feeling the magic yet. To really get a nice looking site with Bootstrap I would still have to go through adding columns, nav bars, headers, footers.... Again, I really wasn't looking to spend all that much time on this. Again, lucky for us, there are Bootstrap themes. 

## Using Bootstrap Themes
Googling around yielded many pre-built Bootstrap themes available for download. After finding one that looked nice I was able to download the pre-built page and work with it. Again, I had to make sure that my dependant files such as CSS and JS were in my 'public' folder. In addition, I also made an 'images' folder inside my 'public' folder to store any images that would be used on the site. This was finally the solution that I was looking for. With a small amount of work, I am now able to make simple web pages that aren't too bad to look at.

<div align="center">
<img src="images/pirate_website_v2.png">
</div>


## Further (Simple) Customization - Pingendo
After doing a bit of work with my first Bootstrap site, Austin Gilmour tipped me off to a program called Pingendo. Pingendo allows the user to visually build a website by dragging and dropping elements into a matt. This allows the user to quickly and easily add/remove objects such as a full width picture, nav bar, header, text sections, etc.... While I haven't yet gotten the chance to use the program, it looks like a great program to try out next. 

<div align="center">
<img src="images/pingendo.png">
</div>


## Pros and Cons of Bootstrap


###Pros:

- Easy to set up and get going
- Developer is not required to be a designer to get a nice looking site off the ground

###Cons:

- Website start to look the same if some one doesn't eventually go in to customize. 
- Re-working the design and keeping it clean and readable can be difficult given the amount of CSS that comes with Bootstrap files. 

## Sources:
- [http://digitalshore.io/development/2015/01/31/bootstrap-pros-cons/](http://digitalshore.io/development/2015/01/31/bootstrap-pros-cons/)
- [https://www.quora.com/What-are-the-pros-and-cons-of-using-Bootstrap-in-web-development](https://www.quora.com/What-are-the-pros-and-cons-of-using-Bootstrap-in-web-development)