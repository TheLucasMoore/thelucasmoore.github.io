---
layout: post
title: Connecting Rails to Angular - Making a Full Stack Web Application
author: Lucas Moore
---

I recently finished my [final project](http://dev.thelucasmoore.com/2016/06/05/The-Spotify-API-and-Angular.html) for Learn Verified! It was an exciting moment, a culmination of everything I've learned in these past four months. My favorite moment came when I realized how to connect my rails database to the front end, which was built with Angular. Below you'll find the steps with lots of code snippets. Peruse the full code [here](https://github.com/TheLucasMoore/rails-angular-final-project).

This article is quite technical and assumes you have working knowledge of rails and angular.

## Connecting the Back End and Front End

<iframe width="560" height="315" src="https://www.youtube.com/embed/Hh2tm5K1kvc" frameborder="0" allowfullscreen></iframe>

A struggle I first had in building the front end of apps with Angular was that I couldn't use rails helpers in my views. The front end and back end aren't automagically connected anymore! To get the information from the database onto the front end, I had to serialize it and use <code>$http.get</code> to grab and render JSON data with Angular.

The most interconnected part of this application is a feature which grabs a user's data from Spotify and then renders a chart of the artist's earnings on the front end ([check out the whole project here](http://dev.thelucasmoore.com/2016/06/05/The-Spotify-API-and-Angular.html)). Here's a breakdown of the process, in eleven easy steps: 

![angular chart of artists](/assets/angular.gif)

## Rails Back End
Here's the steps of the rails part of the app.

# First:

A user authenticates the Spotify API after clicking 'Log Into Spotify', which refers them to <code>/users/auth/spotify</code>

# Second:
Oauth handles the [Authorization Code Flow](https://developer.spotify.com/web-api/authorization-guide/#authorization_code_flow) and returns a secure key to the backend, also logging a user into Devise or creating their account with their Spotify email.

```ruby
# app/controllers/users
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def spotify
    @user = User.from_omniauth(request.env["omniauth.auth"]
    ...
  end
end

# in models/user.rb
class User < ActiveRecord::Base
...
def self.from_omniauth(auth) # grab data for user from Omniauth.
  where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
    user.name = auth.info.name
    user.email = auth.info.email
    user.password = Devise.friendly_token[0,20]
    user.access_token = auth.credentials.token
    user.refresh_token = auth.credentials.refresh_token
    end
  end
```

# Third:

The [RSpotify](https://github.com/guilhermesad/rspotify) gem (which is amazing) grabs the user's top twenty artists, which I sliced to be only the top five artists.

```ruby
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
...
  spotify_user = RSpotify::User.new(request.env['omniauth.auth'])
  top = spotify_user.top_artists.slice(0,5)
  Artist.parse_from_user(@user, top)
  Genre.scrape_genres(top)
  end
end
```

# Fourth:

The models extract the artist and genre data, saving it to the database and associating it with the user. 

```ruby
# Similar logic is used for the genres in the genre model
class Artist < ActiveRecord::Base
  def self.parse_from_user(user, array)
    array.each do |artist|
      newbie = Artist.find_or_create_by(name: artist.name)
      newbie.name = artist.name
      newbie.image = artist.images[0]["url"]
      newbie.popularity = artist.popularity
      newbie.link = artist.external_urls["spotify"]
      newbie.uri = artist.uri
      newbie.followers = artist.followers["total"]
      user.artists << newbie unless user.artists.include?(newbie)
      newbie.save
      user.save
    end
  end
end
```

# Fifth:

Active Model Serializer and controllers render the JSON for each RESTful URL. 

```ruby
# in app/serializers/user_serialzer.rb
class UserSerializer < ActiveModel::Serializer
  attributes :id, :name
  has_many :artists
end

```
Which renders JSON at <code>/user/:id</code> (don't forget to set the show method in the users controller and routes!)

```JSON
{
    "id": 1,
    "name": "lukeymoo",
    "artists": [
    {
        "id": 1,
        "name": "Teebs",
        "image": "https://i.scdn.co/image/787867c011ffcb68f684378c5bdbc7004e71cb55",
        "streams": null,
        "link": "https://open.spotify.com/artist/2L2unNFaPbDxjg3NqzpqhJ",
        "followers": null,
        "uri": "spotify:artist:2L2unNFaPbDxjg3NqzpqhJ"
    }, ...
```

# Sixth:

After all this data is processed, the user is redirected to the <code>#/user/:id</code> page, where we pick up the front end. This required an override of Devise's <code>after_sign_in_path_for</code> method. 

``` ruby
class ApplicationController < ActionController::Base
def after_sign_in_path_for(resource)
    id = resource.id.to_s
    '/#/chart/' + id || root_path
  end
end
```

## The Angular Front End

# Seventh:

The ui-router for a user has a dynamic URL for each user, which is <code>#/user/:id</code>.

```javascript
angular // dependencies are ui.router and chart.js, among others
  .module('app', [
    'ui.router',
    'chart.js',
    ])
    ...
    .state('userChart', {
        url: '/chart/:id',
        templateUrl: 'userChart.html',
        controller: 'UserChartController as user',
      })
```

# Eighth:

The UserChartController grabs the user ID from $stateParams, so it can call the right user's data. This is almost identical to <code>params[:id]</code> in Rails.

```javascript
function UserChartController($scope, $stateParams, BackEndService, LastfmService) {
var userId = $stateParams.id

BackEndService /
    .getUserArtists(userId)
    .then(function(response) {
    ... // what to do with this data
    })
}
```

# Ninth:

The BackEndService calls to the <code>/user/:id</code> and grabs the JSON data for that user, which is all their top artists. Here is the point of connection! This returns the JSON data mentioned above in step five. 

```javascript
function BackEndService($http) {
...
  this.getUserArtists = function(id) {
    return $http.get('http://localhost:3000/users/' + id)
  }
}
```

# And, finally, tenth!

The data that get returned are put into an array of names. For each artist, I call the Last.fm API to grab the number of total listens. 

This is passed to a Chart.js chart, which renders the data on the front page! 
Which is as easy as:

```javascript
function UserChartController($scope, $stateParams, BackEndService, LastfmService) {
...
$scope.labels = names; // names is an array of artist names pulled from the user data
$scope.data = [listens];  //listens is an array of the number of listens.
```

Because [Angular-Chart-JS](http://jtblin.github.io/angular-chart.js/) is so rad, creating that chart is done with calling a canvas object in the view.

See the [full code of the UserCharts controller here](https://github.com/TheLucasMoore/rails-angular-final-project/blob/master/app/assets/javascripts/ng-app/controllers/UserChartController.js).
Or read about this [entire final project here.](http://dev.thelucasmoore.com/2016/06/05/The-Spotify-API-and-Angular.html)


