---
layout: post
title: "Polymorphic Associations"
modified:
categories:
description:
tags: []
image:
  feature:
  credit:
  creditlink:
comments:
share:
date: 2015-11-28T20:42:15-05:00
---
##What are polymorphic associations?

A polymorphic association is when a model can belong to more than one other model on a single association. For example, I have a favorites model that I want to belong to a user and the model that they favorited. Seems simple enough, but maybe the user wants to favorite and instance of the hospital model, or maybe they want to favorite an instance of the community gardens mode. Now you can see how this gets a bit tricky.

<figure align='center'>
  <img src='/images/2015-11-28-polymorphic/polymorphic1.png' title='Before you set up a polymorphic association' alt_text=''><br>
  <figcap>Something isn't right here...</figcap>
</figure>

As we can see from the diagram above, for the Favorite model to belongs_to a User and belongs_to one of multiple possible resources, we're going to need some more columns in the Favorite model. This is an ideal situation for polymorphic associations.

#Setting up Polymorphic Associations

In setting up this association, I am going to assume that you have a User model already set up and the resources that you would like to be able to favorite already set up. Don't worry though, I'll walk you through changes that need to be made in those models to get this all to work.

##Create Model

First, let's create the Favorite model by going to the console and typing in the following:
```
rails g model favorite user_id:integer favoritable_id:integer favoritable_type:string
```

This will generate the following migration for us:

```
t.integer :user_id
t.integer :favoritable_id
t.string :favoritable_type

t.timestamps null: false
```


models/favorite.rb
belongs_to :favoritable, polymorphic: true

models/(each model that's favoritable)
has_many :favorites, as: :favoritable

has_many :course_tas, through: :teaching_assistants, source: :ta_duty, source_type: 'Course'
https://launchschool.com/blog/understanding-polymorphic-associations-in-rails
