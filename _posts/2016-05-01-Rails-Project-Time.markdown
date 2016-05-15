---
layout: post
title: Rails Project Time, Seeds and Testing
author: Lucas Moore
---

For my final project in rails, I put into practice the more complicated aspects of user authentication and authorization. It's a simple recipe tracker, but under the hood it is doing some awesome stuff. Watch this video to get an idea of the web application and then read below to understand how it's built.

<iframe width="420" height="315" src="https://www.youtube.com/embed/705Bob7Z2-E" frameborder="0" allowfullscreen></iframe>

## Authentication with Devise and Omniauth

The user sign up process is built with Devise, which by default only asks for a user's email and password. I added a name and a user's role (like an administrator, moderator or user) to the User table in the database. I also used enum to create user roles in the User model, setting the default to 'user' after initializing a new User. This also gives me methods like <code>User.role</code> and <code>User.admin?</code>, which returns true if the user is an admin.

{%highlight ruby %}
class User < ActiveRecord::Base

 enum role: [:user, :moderator, :admin]

  after_initialize :set_default_user_role

  def set_default_user_role
    self.role = :user unless self.role
  end
end
{%endhighlight%}

I had to modify Devise's default controllers to allow <code>:name</code> and <code>:role</code> in the strong params of the Users::RegistrationsController.

{%highlight ruby %}
class Users::RegistrationsController < Devise::RegistrationsController

  # If you have extra params to permit, append them to the sanitizer.
  def configure_sign_up_params
     devise_parameter_sanitizer.permit(:sign_up){ |u| u.permit(:name, :role, :email,:password,:password_confirmation) }
   end

  # If you have extra params to permit, append them to the sanitizer.
  def configure_account_update_params
     devise_parameter_sanitizer.permit(:account_update){ |u| u.permit(:name, :role, :email,:password,:password_confirmation) }
   end
{%endhighlight%}

I also needed to modify the views for users to show links to delete comments conditionally, based on their role.

{%highlight ruby %}
#Comments.html.erb
...

<% if current_user.admin? || comment.user_id == current_user.id %>
  <%= link_to "Delete", recipe_comment_path(@recipe, comment.id), method: :delete %>
<% end %>
...
{%endhighlight%}

## Authorization with Pundit

[The Pundit gem](https://github.com/elabs/pundit) sets up simple policies that verify whether a user has a particular role before authorizing them to do that action. So if only administrators and moderators can edit comments the <code>CommentPolicy.rb</code> looks like: 

{%highlight ruby %}
class CommentPolicy < ApplicationPolicy

  def edit?
    user.admin? || user.moderator?
  end

end
{%endhighlight%}

It is called by simply adding <code>authorize @comment</code> into *comments_controller.rb*. I also added a custom rescue for Pundit errors in the ApplicationController.

{%highlight ruby %}
class ApplicationController < ActionController::Base

  include Pundit

  rescue_from Pundit::NotAuthorizedError, with: :user_not_authorized

  def user_not_authorized
    flash[:alert] = "Access denied."
    redirect_to(request.referrer || root_path)
  end
end
{%endhighlight%}

So if a user tries to do something they shouldn't be able to do, through some internet wizardry, a notification pops up and says:

![nope](https://media.giphy.com/media/l3V0JykFG4Jqtg7vi/giphy.gif)


## Building Seed Data

To work with the web application in my development environment, I built my own seed data using [the faker gem](https://github.com/stympy/faker). So to create 10 fake users: 

{%highlight ruby %}
#db/seeds.rb
10.times do 
    User.create(
      name: Faker::Name.name,
      email: Faker::Internet.email,
      password: "testtest"
      )
end
{%endhighlight%}

Then when I run <code>rake db:seed</code> I have some users set up to test out permissions with. They've got great names too, like *Constantin Rath V*. I also made seed data for recipes, ingredients and comments. 

## Writing Tests

My favorite part of this project was learning the importance of testing organically. I was trying to build the various user roles and found myself manually signing up users over and over, trying to test the role function. In a [railscast](http://railscasts.com/episodes/275-how-i-test?view=comments) I watched, Ryan Bates mentioned that if you're testing things in the browser, you should probably write a test for that. 

So I used Capybara and Rspec to write a test that signs up a user for each role and then checks if they are signed up in that role. Here's the test for an admin:

{%highlight ruby %}
# spec/features
it "signs up an Admin" do
    visit('/users/sign_up')
    fill_in('Name', :with => 'John Admin')
    fill_in('Email', :with => 'John@inter.net')
    fill_in('Password', :with => 'testtest')
    fill_in('Password confirmation', :with => 'testtest')
    select('admin', :from => 'Role')
    click_button('Sign up')

   expect(page).to have_content 'You have signed up successfully'

    visit('/about')
    expect(page).to have_content 'admin'
  end

{%endhighlight%}

That allowed me to just run the test while I was figuring out the sign up process. When the test passed, I knew it worked without having to manually sign up a new user in all three roles. This is a simple test and I admire the more complex tests used in Test Drive Development. I've learned the importance of writing and having quality tests.

## In Summation

![img](https://media.giphy.com/media/tyxovVLbfZdok/giphy.gif)

I learned a lot during this process and did many a first-pump when things started to function as I invisioned them. The biggest lesson I've learned from this project is about the importance and practicality of Test Driven Development (TDD). That's how Learn is built and how I most enjoy writing programs. 

Please check out the code on [github](https://github.com/TheLucasMoore/rails_final_project) and comment where you see something you like, or that could be improved.