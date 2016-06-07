---
layout: post
title: The Spotify API and Angular
author: Lucas Moore
---

![spotify logo](/assets/logo.png)

## $$$potify: A Full Stack Web Application

For my final project, I built a web application that allows users to estimate how much money an artist has earned on Spotify. I built this because I love music and, honestly, this is a website I would use. I'm proud to know how to make this!

I am so excited about this project. While building it I stopped suddenly and thought, "Wow, I know what I am doing!" The interconnected puzzle of the backend and the front end finally fit together in my mind. I learn best by doing and hacking away at this project was a joy. There were many-a-fist-pumps to be had. I know this is the first of many great web apps I'll build with these new skills.

Check out my walk through video of this application below and read on to see how it's constructed. Check it out on [Github](https://github.com/TheLucasMoore/rails-angular-final-project) too.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Hh2tm5K1kvc" frameborder="0" allowfullscreen></iframe>

## The Rails Back End

The backend is built with Rails and uses Devise, [Omniauth-Spotify](https://github.com/icoretech/omniauth-spotify) and Active Model Serializers. 

A user has many artists, an artist has many users and many genres, and genres have many artists. This many-to-many relationship is stored in two join tables: user_artists and artist_genres.

When a user logs into their Spotify account, they automatically create an account on the website and their top artists are saved to the database. The data for a user's top five artists and their genres are all persisted to the database by methods on their respective models. Here's an example of the <code>Artist.parse_from_user</code> method, which saves artist data from the Spotify API to the database.

```ruby
class Artist < ActiveRecord::Base

# This is called from the OmniAuth Callbacks Controller with the array of top artists
def self.parse_from_user(user, array)
  array.each do |artist|
    newbie = Artist.find_or_create_by(name: artist.name)
    newbie.name = artist.name
    # ... etc
    user.artists << newbie unless user.artists.include?(newbie)
    newbie.save
    user.save
    end
  end
end
```
I strategically chose this structure because I wanted a user to be able to share their charts on social media. This requires the data to be saved to the database, so there is a permalink of the user's chart. Handling Spotify authentication on the backend makes the website more secure as well, as API tokens don't need to be exposed in the front-end code. 

My love for rails deepened while building this full stack app. Ruby is such a graceful language and the rails framework has so many magical little touches. It's a pleasure to build projects with rails and to structure them to fit a front-end framework like Angular JS.

## The Angular Front End

The front end is built with Angular. The bower-rails gem is the package manager for the front end, which blew my mind with its simplicity.

[I used ui-router (great tutorial here)](http://start.jcolemorrison.com/angularjs-rails-4-1-and-ui-router-tutorial/) to manage the page states. Each page has it's own controller which holds the page-specific javascript. These controllers call on specific services to make API requests for each specific API I used. More on APIs below. 

This is a single page application, meaning all the page loads are done instantly through Angular. They don't make requests to the server, causing a page refresh. This gives the app a flawless feel and speedy UI.

# Dependency Injection

In this project, I learned the importance of dependency injection for angular controllers. An error I frequently encountered when calling to a service was something like: 

```javascript
Spotify.getArtists(artist) is undefined
```

After seeing this error a few times I started to get the hang of things. The controller for the home page, which uses the Spotify service, needs to take SpotifyService as an argument. Without it, Angular doesn't know to connect the two, so the method I called returned as undefined.

```javascript
function HomeController(SpotifyService) {
                      // ^^^^ This fixes that error!
  ...
  SpotifyService
    .getArtist(artist)
    .then(function(response) {
      // ...etc
    })
}

angular
  .module('app')
  .controller('HomeController', HomeController)
```
[The full code is on Github.](https://github.com/TheLucasMoore/rails-angular-final-project/tree/master/app/assets/javascripts/ng-app/controllers)

## Calling to APIs

I made use of the Spotify API to pull data for artists and user's top tracks. I supplemented that data with calls to the Last.fm API because Spotify doesn't release the number of steams an artist has on their API, which is [something to consider when building an API](http://thelucasmoore.com/2016/06/01/What-to-consider-when-building-an-api.html). This website isn't a perfect estimate of artist's earnings. It's simply a proof of concept.

So when a user searches for an artist on the front page, two services make calls to Last.fm and Spotify. First, I used the query feature of both APIs to return an array of possible matches from their databases. Then I selected the first response from the array, making an assumption that the closest match is first. It's been right 99% of the time, so far. Please let me know if you find an artist that doesn't render correctly. 

I grab the artist-specific id from the first API get request and then make a second request. That returns the full information for that artist from both APIs. The structure is similar for both, so here's what the Last.fm service looks like: 

```javascript
function LastfmService($http) {
  var url = 'http://ws.audioscrobbler.com/2.0/';
  var API_KEY = '################';

  this.searchFor = function(query) {
    return $http.get(url + "?method=artist.search&artist=" + query + "&api_key=" + API_KEY + "&format=json")
  };
  this.getArtist = function(artist) {
    return $http.get(url + "?method=artist.getinfo&artist=" + artist + "&api_key=" + API_KEY + "&format=json")
  };
}

angular
  .module('app')
  .service('LastfmService', LastfmService)
```

This returns an artist's information in JSON format, which is super tidy and fun. I then extract that information and pass it to the view.

## Wrapping Up

My favorite part of this project was connecting everything I learned in my four months of this coding bootcamp. The backend and the front end finally came together into a full stack app. I understand all the moving parts! I wrote a separate post about connecting the front end and the back end, [which you can read here](http://dev.theluacsmoore.com/2016/06/06/Connecting-Rails-To-Angular-Full-Stack-Web-Development.html) if you want some dense code snippets. 