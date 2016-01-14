---
layout: post
title: "Web Scraping CSS vs. Xpath"
modified:
categories: [css, xpath, scraping]
description:
tags: []
image:
  feature:
  credit:
  creditlink:
comments:
share:
date: 2016-01-14T10:38:46-05:00
---
At The Flatiron School we learned some web scraping. To locate a specific item on the page we used CSS selectors. Recently I have learned about another way to select items on a page called XPath. These two ways of selecting page elements seemed to fill the same function, so I wanted to look and see if one was faster than the other.

First, a quick look at each one.

###Some example html to look at...

{% highlight html linenos%}
<body>
  <div id='primary-title'>
    <h1>Class notes</h1>
  </div>
  <div class='notes'>
    <p>Webtwo ipsum dolor sit amet, eskobo chumby doostang bebo.</p>
    <p>Voki zapier mzinga foodzie spock. Dopplr sifteo rovio.</p>
  </div>
</body>
{% endhighlight %}

##CSS

[CSS selectors](http://www.w3schools.com/cssref/css_selectors.asp) are built to easily select elements with the same syntax you would use for CSS. For example, if I wanted to select an element with 'id=primary-title', all I would have to use is `"#primary-title"`. If I wanted to select all `<p>` tags with 'class=notes', I would have to simply use `"p.notes"`. If you are familiar with CSS, this should all be very familiar to you. An important thing that CSS cannot do that XPath can is move upwards from a given element. This means once I have selected the `<p>` tags with 'class=notes', I cannot go back and access the '<body>' tag without starting from the full page again.

##Xpath

[Xpath selectors](http://www.w3schools.com/xsl/xpath_syntax.asp) select elements on a page (called nodes) in a fashion that more resembles a file structure. You can still do similar searches for elements using class and id, but it doesn't follow the same format. To find an element with 'id=primary-title', you would use `"//*[@id='primary-title']"`. If you wanted to select all `<p>` tags with 'class=notes', you would have to use `"//p[@class='notes']"`. Unlike the CSS selectors, if you have this child node you can move upwards to the parent `<body>` node by using `".."` just like you would in a file structure. If you're looking to start selecting page nodes with XPath, check out [this](https://chrome.google.com/webstore/detail/xpath-helper/hgimnogjllphhhkhlmebbmlgjoejdpjl) Chrome extension, it lets you hover over elements and gives you their XPath.

#Scraping Speed

My main curiosity was to find out if one or the other was faster at scraping a web page. To test this, I created a [website](https://github.com/aturkewi/site-to-scrape) with rails that has a thousand product pages. Each page is very simple and just has the name of the product, the price, and a sku number. I ran the server locally so there would be no variations in bandwidth or in serving the content. I used the HTTParty gem to get the pages from the localhost and the Nokogiri gem to scrape the web pages. I used the built in Benchmark library in Ruby to test speeds.

##Importing HTML and XML with Nokogiri

One difference in the code between scraping with CSS and scraping with XPath is that CSS uses the HTML and XPath uses XML. Because of this, I had to import the product pages differently with nokogiri.

{% highlight ruby%}
# The CSS scraper used:
Nokogiri::HTML(raw_page)

# The Xpath scraper used:
Nokogiri::XML(raw_page)
{% endhighlight %}

To make sure that this would not affect the test, I ran a quick benchmark to make sure that just getting the page with these two methods would yield equal speeds.

<figure align='center'>
  <img src='/images/2016-01-14-web-scraping/img-1-nokogiri-import.png' title='Benchmark import from Nokogiri' alt_text=''><br>
  <figcap>Benchmark import from Nokogiri</figcap>
</figure>

>**READING BENCHMARKS**. The benchmark tests being used in this blog actually run twice. First is the rehearsal (marked as such in the results) and then the actual test done directly below. A rehearsal may be useful to use because if there are a lot of objects that need to be created, the first task that is run may get stuck with some initialization tasks and therefore skew the results. For this reason, the important test to look at is the actual test block run after the rehearsal. The main time you want to pay attention to is the time noted in the parenthesis. This is the total elapsed time to complete the task. For more information on what the other numbers mean how to to use benchmark, check out [this blog](https://wordpress2049.wordpress.com/2015/10/21/how-do-i-benchmark-ruby-code/).

This benchmarks shows that there may be a very small difference in loading the html (about a half second over 51 seconds).

##Running the CSS and XPath Benchmark

For running the actual benchmark, the goal was to scrape through all 1000 product pages and collect an array of hashes that looks like this:

{% highlight ruby%}
[{:name=>"Incredible Linen Lamp", :sku=>"564631335", :price=>"6"},  {:name=>"Enormous Wool Pants", :sku=>"532842039", :price=>"11"}, {:name=>"Lightweight Steel Table", :sku=>"894913920", :price=>"39"}, ...]
{% endhighlight %}

##The results

<figure align='center'>
  <img src='/images/2016-01-14-web-scraping/img-2-benchmark-results.png' title='Benchmark Results' alt_text=''><br>
  <figcap>Benchmark Results</figcap>
</figure>

The results of the actual test show that scraping with Xpath is about 2s faster (over ~57s) than scraping with CSS. Given that we already saw that Nokogiri accounted for .5s, I would conclude that the actual process of scraping with the different selectors amounted to 1.5s.

#Conclusion

When scraping a few pages, the difference in speed between CSS and XPath is pretty small, but when scraping millions of pages, this small amount of time may start adding up. Beyond that case though, my initial conclusion is that scraping pages with either CSS or XPath both work well. XPath has a bit more functionality in terms of moving up the tree and CSS may be easier to pick up if you are already familiar with building web pages in CSS.
