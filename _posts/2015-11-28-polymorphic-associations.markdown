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

{% highlight ruby linenos%}
# db/migrate/123456789_create_favorites.rb
class CreateFavorites < ActiveRecord::Migration
  def change
    create_table :favorites do |t|
      t.integer :user_id
      t.integer :favoritable_id
      t.string :favoritable_type

      t.timestamps null: false
    end
    # Add this line:
    add_index :favorites, [:favoritable_id, :favoritable_type]
  end
end
{% endhighlight %}

Because the favoritable_id and favoritable_type type are often searched together, we went to add an index for them. Make sure to add the line noted above to your migration BEFORE running `rake db:mograte`.

## Add Associations

Now that our all our models are created (again, this is assuming you already have your user model and resource models already migrated) we need to start adding associations. Let's start by going to your Favorite model:

{% highlight ruby linenos%}
# app/models/favorite.rb
class Favorite < ActiveRecord::Base
  belongs_to :favoritable, polymorphic: true
  belongs_to :user
end
{% endhighlight %}

We need to add the two associations shown above. Line number 3 is the polymorphic association that will allow this Favorite model to belongs_to one of many different model options. Line number 4 should look familiar, it is of course saying that each Favorite belongs_to one user.

Good, that was easy! Now we just need to set up the other end of the association. Let's go to one of the models that you want to be able to favorite. For the purpose of this example, let's use Farmers Markets:

{% highlight ruby linenos%}
# app/models/farmers_market.rb
class FarmersMarket < ActiveRecord::Base
  has_many :favorites, as: :favoritable
  has_many :users, through: :favorites
end
{% endhighlight %}

Here on line 3 just adding a has_many association to the favorites model as the keyword `favoritable` that we used earlier. Line 4 is the same has_many through association that we would have used if this were a non-polymorphic relationship. Use these same two lines for ever model that you want to be favoritable.

Now we've set up the favorites model and we know how to set up our resources that we want to favorite. The last step is to set up the through association from our user to our resources.

## Add Through Associations

The goal here is to set up our user model so that it can see it's favorite farmers markets and community gardens without having to do some complicated search like `user.where(user_id:user.id, favoritable_type:'FarmersMarket')`. To get this association we need to do the following:

{% highlight ruby linenos%}
# app/models/user.rb
class User < ActiveRecord::Base
  has_many :favorites
  has_many :farmers_markets, through: :favorites, source: :favoritable, source_type: 'FarmersMarket'
  has_many :community_gardens, through: :favorites, source: :favoritable, source_type: 'CommunityGarden'
end
{% endhighlight %}

On lines 4 and 5 we are simply setting up the association under the name we would be calling it ('user.farmers_markets') and showing the course of how to find it (`through: :favorites, source: :favoritable, source_type: 'FarmersMarket'`).

## Finished Product

That's it! We did it! Take a look below to see the polymorphic association in action. I set the variable `u` equal to a user and added a favorite community garden AND a favorite farmers market.

{% highlight ruby linenos%}
2.2.1 :002 > u.community_gardens
  CommunityGarden Load (1.2ms)  SELECT "community_gardens".* FROM "community_gardens" INNER JOIN "favorites" ON "community_gardens"."id" = "favorites"."favoritable_id" WHERE "favorites"."user_id" = $1 AND "favorites"."favoritable_type" = $2  [["user_id", 1], ["favoritable_type", "CommunityGarden"]]
 => #<ActiveRecord::Associations::CollectionProxy [#<CommunityGarden id: 1, name: "11 BC Serenity Garden", borough_id: 3, size: "0.054", created_at: "2015-11-06 15:57:01", updated_at: "2015-11-06 15:57:01">]>
2.2.1 :003 > u.farmers_markets
  FarmersMarket Load (1.0ms)  SELECT "farmers_markets".* FROM "farmers_markets" INNER JOIN "favorites" ON "farmers_markets"."id" = "favorites"."favoritable_id" WHERE "favorites"."user_id" = $1 AND "favorites"."favoritable_type" = $2  [["user_id", 1], ["favoritable_type", "FarmersMarket"]]
 => #<ActiveRecord::Associations::CollectionProxy [#<FarmersMarket id: 1, name: "Bissel Gardens Farmers' Market", borough_id: 1, day: "Wednesday & Saturday ", created_at: "2015-11-06 15:57:01", updated_at: "2015-11-06 15:57:01">]>
2.2.1 :004 > u.favorites
  Favorite Load (0.6ms)  SELECT "favorites".* FROM "favorites" WHERE "favorites"."user_id" = $1  [["user_id", 1]]
 => #<ActiveRecord::Associations::CollectionProxy [#<Favorite id: 6, user_id: 1, favoritable_id: 1, favoritable_type: "CommunityGarden", created_at: "2015-11-29 16:51:51", updated_at: "2015-11-29 16:51:51">, #<Favorite id: 8, user_id: 1, favoritable_id: 1, favoritable_type: "FarmersMarket", created_at: "2015-11-29 17:10:29", updated_at: "2015-11-29 17:10:29">]>
{% endhighlight %}

You can see that we are able to call both `.community_gardens` and `.farmers_markets` on the user. We can also call `.favorites` and get all of their favorites regardless of type.

I hope this was helpful, and best of luck setting up your own polymorphic associations!

## Sources:

https://launchschool.com/blog/understanding-polymorphic-associations-in-rails
