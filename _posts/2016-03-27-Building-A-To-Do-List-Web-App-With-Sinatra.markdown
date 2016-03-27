---
layout: post
title: Building a To Do List Web App with Sinatra
author: Lucas Moore
---

I am at an exciting point in the boot camp process. The building blocks of the concepts I've learned so far are starting to finally come together!

[Mario Gif]

Learning feels genuinely *FUN*. I had a blast building this latest project, which is the final assessment for the section on Sinatra. 

[Screenshot of my Learn progress]
I have three more (albeit hefty) sections left in the program.

## MVC Structure

[gif of Goodburger]

To build the most usable and readable code, it's common practice to divide a project into models, views and controllers.

So you can understand what I'm talking about, think of a restaurant. You, the user, will walk in, order food and get a plate of food. Hopefully waffles. 
This is like when you go to your favorite Facebased website - you log in, click some stuff and see information. Again, hopefully waffles. 

But the magic of food and websites suddenly appearing before you has a secret process - a Model, a View and a Controller.

[space cat with waffles]

*The model* is the chefs in the back room, chopping up the food, cooking it and flavoring everything. 
This is the bulk of the code that does the work of sorting classes, methods and data structures. 

*The controller* is the waiter who asks what you'd like to eat, listens and then connects your request to the chefs. Then they deliver the food you ordered.
This is where [Sinatra]() comes in to handle the HTTP requests of URL paths, as well as patching and deleting data from the server.

*The View* is the pretty plate of food before you- exactly what you asked for. 
This is the final HTML pages that a user interacts with. In this project, that means the ToDoo web interface. 

Here's each element of the MVC architecture that I built for this application.

[image of the file structure]

## Views

A user needs to either sign up or log in. 

A task needs to be created, edited, viewed and deleted. 

A list also needs to be created, edited, viewed and deleted. 

So each of these functions has its own page with a form that posts data to the database, or a neat little ruby snippet that interjects data from the database into the page.

Here's an example from the view at <code>/lists/new</code> which makes a new list and an associated task. 

{%highlight ruby %}
<h1>New List</h1>
<form method="post" action="/lists/new"> # form code for the controller to handle
  <input type="text" name="name" placeholder="List Name"><br><br>
  <input type="text" name="tasks[name]" placeholder="Add Your First Task"><br><br>
  <input class="btn btn-primary" type="submit" name="create" value="Create">
</form>
{%endhighlight%}

These <code>.erb</code> pages also inheret their styling from a <code>layout.erb</code> document that pulls stylesheets for Bootstrap and FontAwesome.

## Models

I envisioned a simple To Do list application, which means it should have a *user*, that can make a *list* and add *tasks* to each list. 

There is a model for User, for List and for Task. Each inherits from [ActiveRecord](), which handles the persistence of data to the database. I was building each of the handy methods that come baked in with ActiveRecord from scratch before this, so I also understand the magic behind what ActiveRecord accomplishes. But gosh, it feels good to see the magic just work. 

A List has many tasks and belongs to a specific user.
A Task also belongs to a specific user. 
A User has many lists and many tasks through lists. 

Finally, a user has a secure password, which is built with [bcrypt]().

Check [this project out on Github]() to see the database migrations and the structure of the tables that hold these data. 

## Controllers

The function of controllers is divided up into a users controller, a task controller and a list controller. 

Take a look at this piece of ListsController.rb

{%highlight ruby %}
get '/lists/new' do
  redirect_if_not_logged_in
  erb :'/lists/new'
end

post '/lists/new' do
  list = List.create(:name => params[:name], :user_id => session[:user_id])
  task = Task.create(:name => params[:tasks][:name])
  list.tasks << task unless list.tasks.include?(task)
  redirect '/tasks'
end
{%endhighlight%}

When I navigate to Foo.com/lists/new, this loads the view lists/new.erb, which has the form to make a new list and task, as mentioned above. 
That form posts the information the user enters at */lists/new* and creates list and task objects. It then pushes the task into the associates list.tasks.
Finally, the user is redirected to the /tasks page, which shows their lists of tasks. 

## A Walk Through

In this video, I'll walk you through all the features of this web app and explain the inner workings. 






