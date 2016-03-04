---
layout: post
title: "Advanced Active Record Queries"
modified:
categories:
description:
tags: []
image:
  feature: 2015-12-20-rails-g-cover.jpg
  credit:
  creditlink:
comments:
share:
date: 2016-03-03T14:30:22-05:00
---
When I went through the immersive course at The Flatiron School, I found that learning and using some advanced Active Record queries was difficult. I am now in my first week as an online web instructor for The Flatiron School and the first study group that I sat in on was going through this very topic. It is still confusing.

I want to go over some tips and tricks I found useful as well as a few examples.

## Understanding what Active Record Queries and Arel Are

Arel is a library that active record uses to allow us developers to write seemingly simple lines of code that are then converted into some database query language such as SQL. Another thing that is really nice about it is that we can write our query with Active Record and Arel will take care of converting our query to the language that is used by the database.

Here is an example. If we have a class User that has_many of a class Product, then we can write this Active Record query, `User.first.products.order(:name)`, to get all of a user's products sorted by name. Arel will then convert that to 2 SQL queries for us. Take a look at the example below:

{% highlight ruby %}
2.2.0 :004 > User.first.products.order(:name)
  User Load (0.3ms)  SELECT  "users".* FROM "users"  ORDER BY "users"."id" ASC LIMIT 1
  Product Load (0.7ms)  SELECT "products".* FROM "products" INNER JOIN "line_items" ON "products"."id" = "line_items"."product_id" INNER JOIN "carts" ON "line_items"."cart_id" = "carts"."id" WHERE "carts"."user_id" = ?  ORDER BY "products"."name" ASC  [["user_id", 1101]]
 => #<ActiveRecord::AssociationRelation [#<Product id: 327, name: "Ergonomic Granite Pants", price: 625, created_at: "2016-03-01 15:30:27", updated_at: "2016-03-01 15:30:27">...
{% endhighlight %}

You can see this happen by running the command in your terminal.

If we take a look, we can see that the first SQL query is the `User.first` part of our Active Record Query. This query gets all of the columns (`SELECT  "users".*`) from our Users table (`FROM "users"`). It then orders them by their ID (`ORDER BY "users"."id"`), and then grabs the first one (`ASC LIMIT 1`).

Active Record and Arel then build the next part of our line of code, `.products` (`SELECT "products".* FROM "products"`) to select products. It automatically joins our users table to our products table (`INNER JOIN "line_items" ON "products"."id" = "line_items"."product_id" INNER JOIN "carts" ON "line_items"."cart_id" = "carts"."id"`). It then finds all products linked to the given user, and sorts them alphabetically (`WHERE "carts"."user_id" = ?  ORDER BY "products"."name" ASC  [["user_id", 1101]]`).

This gives us a quick idea of how powerful Active Record Queries and Arel can be.

## What Active Record Queries Returns

By default, Active Record Queries only return an array (or more accurately an ActiveRecord::Associations::CollectionProxy) or an instance of the object you called the search on. Here is a quick example to demonstrate this point.

If I want all the data from the first product in my products table, I can do this:
{% highlight ruby %}
2.2.0 :056 > Product.select("products.*").first
  Product Load (2.1ms)  SELECT  products.* FROM "products"  ORDER BY "products"."id" ASC LIMIT 1
 => #<Product id: 318, name: "Gorgeous Plastic Car", price: 298, created_at: "2016-03-01 15:30:27", updated_at: "2016-03-01 15:30:27">
{% endhighlight %}

 That's great, that's just what I wanted and exactly what I would expect it to return. But what if I just want the name? I would probably try the following:
 {% highlight ruby %}
 2.2.0 :058 > Product.select("products.name").first
   Product Load (1.0ms)  SELECT  products.name FROM "products"  ORDER BY "products"."id" ASC LIMIT 1
  => #<Product id: nil, name: "Gorgeous Plastic Car">
{% endhighlight %}

 Hmmmm. I got the name, which is what I wanted, but Active Record returned to me a Product object that's just missing the other data.

 Ok, let's try something a little different. Now let's try getting a list of product's prices that a User bought. I would try something like this:

 {% highlight ruby %}
 => #<ActiveRecord::Relation [#<User id: nil, name: "Ergonomic Granite Pants">, #<User id: nil, name: "Incredible Granite Computer">, #<User id: nil, name: "Gorgeous Rubber Gloves">, #<User id: nil, name: "Sleek Plastic Gloves">, #<User id: nil, name: "Gorgeous Cotton Pants">, #<User id: nil, name: "Small Steel Chair">, #<User id: nil, name: "Incredible Wooden Hat">, #<User id: nil, name: "Sleek Rubber Gloves">]>
2.2.0 :063 > User.select("products.price").joins(carts: { line_items: :product}).where("users.id = ?", 1101)
  User Load (1.1ms)  SELECT products.price FROM "users" INNER JOIN "carts" ON "carts"."user_id" = "users"."id" INNER JOIN "line_items" ON "line_items"."cart_id" = "carts"."id" INNER JOIN "products" ON "products"."id" = "line_items"."product_id" WHERE (users.id = 1101)
 => #<ActiveRecord::Relation [#<User id: nil>, #<User id: nil>, #<User id: nil>, #<User id: nil>, #<User id: nil>, #<User id: nil>, #<User id: nil>, #<User id: nil>]>
{% endhighlight %}

Ok, a bunch of nil User objects... What did my query even return? It's clear that whatever I wanted, Active Record is only going to return to me User objects here. It's important to keep in mind here that when you make a SQL query, you're generally specify a table that you want returned (if you're unfamiliar with this, I recommend checking out [Khan Academdy's SQL course](https://www.khanacademy.org/computing/computer-programming/sql)). Active Record is simply trying to fit the table returned into the object type you're calling the query on.

>Note: My general rule of thumb is that if I want my return value to be ObjectX, then call my query on ObjectX as well.

Because of this, it can sometimes be difficult for us to tell what table our SQL query is really creating. It would be really nice if we can see that...

## Viewing The SQL Query in a GUI

To solve this problem, I sometimes like to look at my SQL queries in a GUI (Graphical User Interface). Currently I use [SQLiteBrowser](http://sqlitebrowser.org) . Using this program I can run my raw SQL queries to see what I'm really getting back.

Looking up to the previous example where I wanted a list of product prices, you can see in the code snippet that the console actually outputs the raw SQL query. If I take that query and put it into SQLiteBrowser, I can now see the table that the database is returning:

<figure align='center'>
  <img src='/images/2016-03-03-active-record/img-1-sqlbrowser.png' title='SQLBrowser Output' alt_text=''><br>
  <figcap>SQLBrowser Output</figcap>
</figure>

This very clearly shows me that my SQL query created by Active Record and Arel returns the above table. The above table was then converted into User objects. Because there is no way to add `price:` to a User, they all came back nil and we were none the wiser to the table that our SQL returned. You'll even notice that there were 8 nil User objects returned and SQLiteBrowser shows in the bottom box that there are 8 rows returned here. Clearly there was one nil User returned for each Product price. SQLiteBrowser can be used anytime you are confused about what data you are actually calling back from the database when you are writing complex queries.

## Advanced Queries

Here are some advanced queries that are sometimes a bit tricky.

### `#pluck()`

One of the Learn students, [Brett Heenan](https://github.com/ryuichi7) brought my attention to the `#pluck()` method. This will pull out specified attributes from an Active Record query. This method would be perfect for the previous example where I was trying to get all of the products prices owned by a user. Let's give it a try:

{% highlight ruby %}
2.2.0 :064 > User.select("products.price").joins(carts: { line_items: :product}).where("users.id = ?", 1101).pluck('products.price')
   (7.4ms)  SELECT products.price FROM "users" INNER JOIN "carts" ON "carts"."user_id" = "users"."id" INNER JOIN "line_items" ON "line_items"."cart_id" = "carts"."id" INNER JOIN "products" ON "products"."id" = "line_items"."product_id" WHERE (users.id = 1101)
 => [625, 612, 658, 101, 166, 102, 893, 923]
{% endhighlight %}

If we take a look, we can see that the raw SQL queries are exactly the same! Pluck allows use to specify what data we want to return from the table that the SQL query created.

### `sum()`

To change tracks a bit here, I've always found it difficult to get values that were grouped and summed from these advanced SQL queries. Lets try and get all of the users that have spend more than 5600 on products.

My first step here is to get users and products on the same table together. I know I'm looking to get back Users as my return value, so I'll be calling this query on User. I also know that all I need from my products table will be price (I don't care about the name of the product). This is a complex query, so let's just start by trying to get this to show up in SQLiteBrowser first. (I'm using my terminal to convert my Active Record query into SQL). I decided to order my results by user's name as well so that I'll be able to see all their rows together.

{% highlight ruby %}
2.2.0 :075 > User.select("users.*, products.price").joins(carts: { line_items: :product}).order("users.name")
  User Load (14.3ms)  SELECT users.*, products.price FROM "users" INNER JOIN "carts" ON "carts"."user_id" = "users"."id" INNER JOIN "line_items" ON "line_items"."cart_id" = "carts"."id" INNER JOIN "products" ON "products"."id" = "line_items"."product_id"  ORDER BY users.name
  => #<ActiveRecord::Relation...
{% endhighlight %}

<figure align='center'>
  <img src='/images/2016-03-03-active-record/img-2-sum1.png' title='Step 1 to Summing' alt_text=''><br>
  <figcap>Users joined to Products showing User info and Product price </figcap>
</figure>

This is a good starting point. Next we know that as we are interested in price total per user, we know we will have to group our data by user. So lets add a `.group("users.id")` to our query.

{% highlight ruby %}
2.2.0 :076 > User.select("users.*, products.price").joins(carts: { line_items: :product}).group("users.id").order("users.name")
  User Load (5.7ms)  SELECT users.*, products.price FROM "users" INNER JOIN "carts" ON "carts"."user_id" = "users"."id" INNER JOIN "line_items" ON "line_items"."cart_id" = "carts"."id" INNER JOIN "products" ON "products"."id" = "line_items"."product_id" GROUP BY users.id  ORDER BY users.name
  => #<ActiveRecord::Relation...
{% endhighlight %}

<figure align='center'>
  <img src='/images/2016-03-03-active-record/img-3-sum2.png' title='Step 1 to Summing' alt_text=''><br>
  <figcap>Grouping by user.id</figcap>
</figure>

Awesome, we're making some progress as our users are all grouped together but the `price` column no longer makes sense. It looks like it's only showing the price of the _last_ item a user bought. Now it's time for us to use sum. Now we don't want the sum of a whole column, but rather the sum for a given user (or row). To do this we add the call to sum to the SELECT portion of the Active Record query just as we would have done with a raw SQL query. I'm also going to add a name for this column so that we can easily manipulate it later if we need to. Our select method now reads `select("users.*, sum(products.price) as 'users_total'")`. Let's see what this does:

{% highlight ruby %}
2.2.0 :077 > User.select("users.*, sum(products.price) as 'users_total'").joins(carts: { line_items: :product}).group("users.id").order("users.name")
  User Load (5.2ms)  SELECT users.*, sum(products.price) as 'users_total' FROM "users" INNER JOIN "carts" ON "carts"."user_id" = "users"."id" INNER JOIN "line_items" ON "line_items"."cart_id" = "carts"."id" INNER JOIN "products" ON "products"."id" = "line_items"."product_id" GROUP BY users.id  ORDER BY users.name
  => #<ActiveRecord::Relation...
{% endhighlight %}

<figure align='center'>
  <img src='/images/2016-03-03-active-record/img-4-sum3.png' title='Step 1 to Summing' alt_text=''><br>
  <figcap>Sum price</figcap>
</figure>

Woo! We now have the total each customer spent. Now we just have to say that we only want the ones that spend more than 5600. Lets also order it by their total_spent in descending order. To do this, we just have to get rows `having("users_total > 5600")`. (See why it was useful to name that column now?).

>Note: Just as with raw SQL, when using `#group()` you have to filter by using `#having()`. If you are not using group, then you can filter by using `#where()`

{% highlight ruby %}
2.2.0 :081 > User.select("users.*, sum(products.price) as 'users_total'").joins(carts: { line_items: :product}).group("users.id").order("users_total desc").having("users_total > 5600").order("users_total desc")
  User Load (3.4ms)  SELECT users.*, sum(products.price) as 'users_total' FROM "users" INNER JOIN "carts" ON "carts"."user_id" = "users"."id" INNER JOIN "line_items" ON "line_items"."cart_id" = "carts"."id" INNER JOIN "products" ON "products"."id" = "line_items"."product_id" GROUP BY users.id HAVING users_total > 5600  ORDER BY users_total desc
 => #<ActiveRecord::Relation [#<User id: 1134, name: "Marguerite Flatley", created_at: "2016-03-01 15:30:27", updated_at: "2016-03-01 15:30:27">, #<User id: 1131, name: "Mr. Elda Ritchie", created_at: "2016-03-01 15:30:27", updated_at: "2016-03-01 15:30:27">, #<User id: 1182, name: "Ms. Annabel Nienow", created_at: "2016-03-01 15:30:27", updated_at: "2016-03-01 15:30:27">, #<User id: 1124, name: "Hipolito Wiegand", created_at: "2016-03-01 15:30:27", updated_at: "2016-03-01 15:30:27">]>
{% endhighlight %}

<figure align='center'>
  <img src='/images/2016-03-03-active-record/img-5-sum4.png' title='Step 1 to Summing' alt_text=''><br>
  <figcap>Filter out only high rollers and sort by users_total</figcap>
</figure>

All right, we did it! We wrote a complex Active Record query to get all of the users that spent more than 5600.

## Closing Thougts

Some important tips to remember when crafting complex Active Record queries:

 - Take it step by step, you don't have to write the whole thing out in one go
 - If you're not sure what your query is actually returning, look at it in a GUI
 - If you are more comfortable with raw SQL, it's OK to start by writing that out first and getting it working and then trying to work your way back to the Active Record syntax.
