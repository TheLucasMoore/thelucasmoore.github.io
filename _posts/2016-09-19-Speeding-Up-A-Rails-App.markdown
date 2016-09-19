---
layout: post
title: Speeding Up Rails Apps and ActiveRecord
author: Lucas Moore
---

A vanilla Rails app will take you quite far. But when it's time to identify and optimize a specific endpoint, there are a few tricks to speed things up.

## Indexes on the Database

Adding an index in ActiveRecord will created an organized list of that table row, actually a [binary search tree](http://dev.thelucasmoore.com/2016/09/16/Data-Structures-in-Ruby.html), which dramatically increases the query time. There's a slight hit on performance with insertion times, but the payoff is well worth it. [Here's a great article about indexing](http://rakeroutes.com/blog/increase-rails-performance-with-database-indexes/).

It's worth noting that you shouldn't index everything in a database. Identify the main data points you'll need access too, structured around your queries, and index those. Indexing everything defeats the point of indexing and will possibly slow your app down.

## Identify and Resolve N+1 Queries

An N+1 query comes when hitting the database for information and then for associated information. An example ([from this great article](https://blog.codeship.com/speed-up-activerecord/)) would be:

```ruby
#app/views/restaurants/index.html.erb
<% @restaurants.each do |restaurant| %>
  <tr>
    <td><%= restaurant.name %></td>
    <td><%= restaurant.review_average %></td>
    ...
```

For each restaurant, we grab the name and then grab the reviews. For as many restaurants we have (N), we'll also need this additional request (N+1). We can speed this up with eager loading the associations, using `.includes`. No extra database query required, as the associations are already loaded.

```ruby
def index
  @restaurants = Restaurant.all.includes(:reviews)
end
```

## Build out Custom SQL Queries for a Request

In certain contexts, its worth dropping down to SQL for custom database queries. This can be done with the built-in Rails helpers:

```ruby
@restaurants = Restaurant.all
  .select("restaurants.*, AVG(reviews.rating) AS review_average")
  .joins(:reviews)
  .group("restaurants.id")
```

It can also be useful in highly specific contexts to utilize the `.find_by_sql` and write out a SQL query manually, to grab so precisely the data we're looking for.

```ruby
@restaurants = Restaurant.find_by_sql(["
  SELECT  restaurants.*, COUNT(reviews.id) AS review_count
    FROM `restaurants`
      INNER JOIN `reviews` ON `reviews`.`restaurant_id` = `restaurants`.`id`
    WHERE (reviews.created_at > ?)
    GROUP BY restaurants.id
    HAVING COUNT(reviews.id) > 10
    LIMIT 10", 3.months.ago])
```

By the way, [credit for these great snippets](https://blog.codeship.com/speed-up-activerecord/) goes to Codeship.

## Optimize Imports with the ActiveRecord-Import Gem

For bulk insertions of data into ActiveRecord, calling on `.create` during iteration can be quite taxing on both time and memory. The [activerecord-import gem](https://github.com/zdennis/activerecord-import) will gather all the data and optimize the import automatically. So what could be a few dozen requests can suddenly become one SQL request.

## What Else?

What sort of things have you found to be useful in speeding up a Rails application? Let me know on [twitter](https://twitter.com/thelucasmoore)!
