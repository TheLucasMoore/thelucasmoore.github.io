---
layout: post
title: Using CLI Generators in Rails and Angular 2
author: Lucas Moore
---

This week I went to an [Angular meetup](http://www.meetup.com/RockyMountainAngularJS/) in Denver, where we learned about the Angular CLI. There are some interesting parallels between the CLI generators for Rails, so I'll review the Rails generators and explain what the Angular CLI generators do too. 

## Generators in Rails

I wrote about the [MVC](http://dev.thelucasmoore.com/2016/03/27/Building-A-To-Do-List-Web-App-With-Sinatra.html) structure when I built a To Do List with Sinatra. The command line is a powerful place to automate the building of models, views or controllers. You can read the full [rails command line documentation here](http://guides.rubyonrails.org/command_line.html).

In order of most powerful to most nuanced, here's the rails generators I've used. 

# 1. Rails Generate Scaffold
  - makes database migrations
  - makes models
  - makes views (with CRUD functionality)
  - makes routes
  - makes test suite
  - makes assets like javascript and CSS files

This is going to make everything you need, but it's not a good idea to use in production. You'll spend too much time cleaning up all the code this makes for you. Below I've pulled out the parts of the code that are interesting, ignoring the additional javascript, CSS assets and testing suites. But there you go. One line of code and you have almost a full CRUD application. 

``` shell
// ♥ rails g scaffold task title:string content:string

Running via Spring preloader in process 17379
  invoke  active_record
  create    db/migrate/20160702153159_create_tasks.rb
  create    app/models/task.rb
  route    resources :tasks
  create    app/controllers/tasks_controller.rb
  create      app/views/tasks
  create      app/views/tasks/index.html.erb
  create      app/views/tasks/edit.html.erb
  create      app/views/tasks/show.html.erb
  create      app/views/tasks/new.html.erb
  create      app/views/tasks/_form.html.erb
```

# 2. Rails Generate Model
  - makes a model
  - makes a migration
  - makes the test suite

Moving into more useful and exacting generators, the model generator will make a model and the associated database migration. 

``` shell
// ♥ rails g model user name:string
Running via Spring preloader in process 17525
  invoke  active_record
  create    db/migrate/20160702154345_create_users.rb
  create    app/models/user.rb
  invoke    test_unit
  create      test/models/user_test.rb
  create      test/fixtures/users.yml
```
An awesome, almost *magical* feature here is that the migration file is written for us: 

``` ruby
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name
      t.timestamps null: false
    end
  end
end
```

# 3. Rails Generate Controller
  - makes a controller
  - makes an empty view folder
  - makes helpers
  - makes assets like javascript and CSS files

Generating a controller will just make the controller and the views folder if it doesn't exist yet. 

``` shell
// ♥ rails g controller user
Running via Spring preloader in process 17579
  create  app/controllers/user_controller.rb
  invoke  erb
  create    app/views/user
  invoke  test_unit
  create    test/controllers/user_controller_test.rb
  invoke  helper
  create    app/helpers/user_helper.rb
  invoke    test_unit
  invoke  assets
  invoke    coffee
  create      app/assets/javascripts/user.coffee
  invoke    scss
  create      app/assets/stylesheets/user.scss
```

# 4. Rails Generate Migration

When needing to change the structure of our database, generating a migration is our best bet. You can follow the naming convention, which will recognize the table you want to change and insert your structure into a new migration. This structure of <code>AddSomethingToTablename</code> is powerful stuff. Also, in [Rails 5](http://weblog.rubyonrails.org/2016/6/30/Rails-5-0-final/), you'll run the migrations with <code>rails db:migrate</code> instead of <code>rake db:migrate</code>, which cuts out the binary seperation between rails and rake tasks. Very cool, eh?

``` shell
// ♥ rails g migration AddNicknameToUsers nickname:string
Running via Spring preloader in process 17630
      invoke  active_record
      create    db/migrate/20160702160315_add_nickname_to_users.rb
``` 

Our database migration looks like this: 

``` ruby 
class AddNicknameToUsers < ActiveRecord::Migration
  def change
    add_column :users, :nickname, :string
  end
end
``` 

## The Angular CLI Generators

Using Angular 2 and the associated generators is an equally powerful tool to create most of the code you'll need for an Angular project. 

# Getting Set Up

To get started with the Angular CLI, let's install it, then use it to generate a new project. Then you'll have to CD into it. 

``` shell 
// ♥ npm install -g angular-cli@latest
// ♥ ng new PROJECT_NAME
// ♥ cd PROJECT_NAME
``` 
Some of the handy-dandy features of the Angular CLI are almost identical to Rails CLI tools. They include: 

  1. <code>ng serve</code> - this runs an angular aware server on the local machine so you can see your project in development.

  2. <code>ng test</code> - runs your jasmine tests.

  3. <code>ng e2e</code> - runs end to end tests with protractor.

  4. <code>ng lint</code> - this lints your project, based on [John Papa's style guide](https://github.com/johnpapa/angular-styleguide). It passes or it doesn't!

  5. <code>ng build</code> - this will build your project, compile your SCSS and minify your javacript into the output path (distribution folder by default).

  6. <code>ng help</code> - need some help? the help menu will explain all this stuff for you again. 

The generator features of Angular are pretty straight-forward. 

# 1. Angular Generate Components
  - makes a component
  - makes a template for the component
  - wires up the tests
  - makes the routes needed

You can make a new component, but it won't be entirely wired up yet. To finish this up, you'll have to manually import this new component into your root component. The component generator does also generate the needed routes, so there's no need to generate a route after this. 

``` shell 
// ♥ ng generate component a-new-comp
installing component
  create src/app/a-new-comp/a-new-comp.component.css
  create src/app/a-new-comp/a-new-comp.component.html
  create src/app/a-new-comp/a-new-comp.component.spec.ts
  create src/app/a-new-comp/a-new-comp.component.ts
  create src/app/a-new-comp/index.ts
```

# 2. Angular Generate Services
  - makes a new service
  - wires up the tests for this service

This one is simple. Make a service, then you can wire it up with $http and do all your requests and posts through the snazzy service. I used services in my [$$$potify](www.ssspotify.com) project to handle all the API requests to the rails backend, as well as to the Spotify and Last.fm APIs. 

``` shell
// ♥ ng generate service a-fresh-service
installing service
  create src/app/a-fresh-service.service.spec.ts
  create src/app/a-fresh-service.service.ts
```

# 3. Angular Generate Routes
  - makes a new route
  - makes a new component if one doesn't exist
  - wires up the testing framework

This one also is not entirely wired into your project, but the files needed are created. Note that a + denotes a lazy-loading route, which can greatly speed up your project. 

``` shell
// ♥ ng generate route foo-route
installing route
installing component
  create src/app/+foo-route/foo-route.component.css
  create src/app/+foo-route/foo-route.component.html
  create src/app/+foo-route/foo-route.component.spec.ts
  create src/app/+foo-route/foo-route.component.ts
  create src/app/+foo-route/index.ts
  create src/app/+foo-route/shared/index.ts
```

# Conclusion

So these two frameworks have some powerful generators. We can produce the frameworks of all the parts needed to make a backend in rails and a single-page application in Angular on the front end. There's a bit of wiring up and connecting still needed, but these generators can greatly increase the speed of your workflow. 
