---
layout: post
title: Deploying Angular Apps to Heroku, Lessons Learned
author: Lucas Moore
---

As I have been putting the finishing touches on my final project, I'm preparing to deploy it on [Heroku](https://devcenter.heroku.com/start) to share with the world! To accomplish this, there are some changes I've had to make in my code. 

Errors are my favorite. Debugging a program requires part intuition and part creativity. So when I first attempted to deploy my [final project](http://dev.thelucasmoore.com/2016/06/05/The-Spotify-API-and-Angular.html) to Heroku I was met with a few errors, which I learned how to fix.

## Moving from SQLite3 to a Postgres Database

Heroku doesn't play well with SQLite, so when I first tried to deploy my application, the build failed. The messages informed me that I needed to switch my database from [SQLite3 to Postgres](http://railscasts.com/episodes/342-migrating-to-postgresql). That process was simple. I updated my gemfile, ran <code>bundle update</code> and then ran a [postgres server](http://postgresapp.com/) on my local machine. Rake allows me to drop and migrate the database quite simply, so this wasn't a huge issue. My migrations all kept the database structure intact on the new server.

## SASS Compiling Error

A simple syntax error broke my entire build! I forgot a colon in one of my SASS files. As the SCSS was compiled into CSS, that error caused issues. Luckily, the log showed me the exact file and line number, so I went back and revised it. Oops. Shipping code into production is a serious endeavour.

## Application Build Failure

Ahh! One on deploy, I got this error. 

![heroku error](/assets/app-error.png)

I [found a solution](http://stackoverflow.com/questions/13496827/heroku-deployment-error-h10-app-crashed), which was to run <code>$ heroku run rails console</code>. This spit out a more verbose error message than what I saw in the Heroku log. It revealed some code I had uncommented in an attempt to create a user session earlier. It wasn't actually needed, so commenting it out again fixed the problem.

## Undefined Method from Minification of Javascript

The biggest struggle was adapting Angular to work after going through minification. In my project, one of the controllers looks like this:

```javascript
function GenreController($stateParams, BackEndService) {
                      // ^^^ Dependency Injection!
  var ctrl = this;
  var id = $stateParams.id
  var init = function() {
    BackEndService
      .getGenre(id)
      .then(function(response) {
        console.log(response)
        ctrl.name = response.data.name
        ctrl.artists = response.data.artists
      });
  };
  init()
};
angular
  .module('app')
  .controller('GenreController', GenreController)
```

Now when compiling the javascript for deployment, it becomes minified, meaning the code now looks something like: 

![minified code](/assets/minified.png)

I mentioned [dependency injection](http://dev.thelucasmoore.com/2016/06/05/The-Spotify-API-and-Angular.html) in my writeup of my final project. I learned lots about the quirks of Angular in that process. The issue here is when the code becomes minified, it replaces the names of the dependecies with letters. This makes the code smaller and therefore quicker. But Angular doesn't know which dependencies are which. Hence, more errors.

To override this, I explicitly wrote out the injections for each controller like this: 

```javascript
GenreController.$inject = ['$stateParams', 'BackEndService'];
```
That did the trick! Check out my final project right here. 