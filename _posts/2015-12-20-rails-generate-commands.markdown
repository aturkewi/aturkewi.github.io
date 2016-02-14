---
layout: post
title: "Rails Generate Commands"
modified:
categories: [rails, ruby, rails generate]
description:
tags: []
image:
  feature: 2015-12-20-rails-g-cover.jpg
  credit:
  creditlink:
comments:
share:
date: 2015-12-20T08:55:31-04:00
---
There are a lot of different options when generating a new part of your website through `rails generate` (`rails g` for short). Our options are `rails g scaffold`, `rails g resource`, `rails g model`, `rails g controller`, and `rails g migration`. In this blog post I want to go over what each generate command does so you can best decide which is right for your need.

<figure align='center'>
  <img src='/images/2015-12-20-rails-g/img-1-rails-g.png' title='Rails Generate Table' alt_text=''><br>
  <figcap>Rails Generate Table</figcap>
</figure>

This table gives a basic overview of what what primary files are created (note, I left out the testing files that are created).

Mose of these rails commands can be used in their simplest form by typing in the command followed by the model name, followed by their database columns and types. Here is an example `rails generate resource post title:string body:text published:boolean`. For `scaffold`, `resource`, and `model`, the resource name is singular. The exceptions to how this is used are `controller` and `migration`. Generating a controller uses camel case and uses the plural and generating a migration will be more wordy. I'll go over each of these in their sections. If you ever forget, you can always just type in `rails g ?` where `?` is the name of the command with no following text and rails will follow with examples and explanations of how to use that command.  

## `rails g scaffold`

From the table, we can see that scaffold probably provides us with the most material compared to the other commands. While it does create all the same files as a 'resource', the big difference is that scaffold fills in views and the controller. This command and the `resource` command both give us the following line in our routes.rb file: `resources :posts`. This just gives the user access to all this pre-built good-ness right off the bat.

As far as views, scaffold gives you a 'new', 'edit', 'show', and 'index', complete with delete buttons. It is also worth noting that the scaffold command builds the 'new' and 'edit' page with another view file, a form partial. The top of the form partial generated from `rails g scaffold Post name age:integer` is shown below:

{% highlight ruby linenos%}
# app/views/posts/_form.html.erb

<%= form_for(@post) do |f| %>
  <% if @post.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@post.errors.count, "error") %> prohibited this post from being saved:</h2>

      <ul>
      <% @post.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
...
...
{% endhighlight %}

Even if you don't want to have all the fields you created in your DB on the form, you probably want to be able to at least return errors to the user if there is an error with their submission.

The other nice thing about the scaffold is that it generates all of the basic CRUD actions in the controller along with the option of returning JSON rather than HTML.

Scaffold really does a lot for you, but it can easily be overkill. Let's take a look at some other options as well.

## `rails g resource`

Creating a resource is very similar to creating a scaffold, but without a filled in views or controller. As far as the views go, it just gives you an empty folder and the controller just gives you a file that correctly inherits from Active Record. This is a good tool if you know you are going to want to have an object that the user will be able to access, but don't automatically want all the CRUD actions laid out via rails convention.

## `rails g model`

A model creates even fewer files for us. All we get is the migration and the model file. This is better if you are building an object that you want stored in the DB, but that you may not want the user to directly access. Another reason for using this option is if you would prefer to be more granular in the constructions of your site.

## `rails g controller`

Generating a controller is a little bit different than everything we've looked at so far. The generate command uses CamelCase and is plural. Also, the options that you pass to the generate command are the method names you want in the controller. Here is an example `rails generate controller CreditCards open debit credit close`. Only the specified routes are added to the router and as this doesn't follow the most standard rails convention, the views it creates are just one or two lines stating their location:

{% highlight html linenos%}
<h1>CreditCards#credit</h1>
<p>Find me in app/views/credit_cards/credit.html.erb</p>
{% endhighlight %}

Generating a controller would be great for something that you want to show, but may not exist in the database. Another good tool for this is to use it in conjunction with model so you can get more control of the routes created for you.

## `rails g migration`

Finally, we have the migration. This command creates the fewest files for you but it can be the trickiest to use. All this command does is create a migration file, but depending what you pass to it, you can create different types of migrations.

### Create a new table

To create a new table use something like`rails g migration CreatePosts name:string age:integer`. The first thing you pass is CamelCase, starts with the word 'Create' and has the model you want to create as a plural. After the create, it works just like any of the other generate commands where we use `column_name:data_type`.

### Add or remove a column

To add or remove columns you do something like `rails g migration AddContentToPosts content:text`. The general format here is `AddXXXToYYY` or `RemoveXXXFromYYY` followed by the columns you want to add or remove. The command written above for adding a content column to posts generates the following migration file:

{% highlight ruby linenos%}
class AddContentToPosts < ActiveRecord::Migration
  def change
    add_column :posts, :content, :text
  end
end
{% endhighlight %}

## Closing thoughts on `rails g`

So those are the basic uses for the rails generate commands. Some important things to think about/remember:

* You can always run the generate command and then go in an make change for things that didn't come out exactly correct (most useful for me with migrations).
* Think about what method works best for you. Do you want to start small and build up your files? Or do you want to create everything and then cut files back?
