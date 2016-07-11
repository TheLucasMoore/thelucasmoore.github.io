---
layout: post
title: Building Slack Bots with Node
author: Lucas Moore
---

My project this past weekend was to create a SlackBot that my brother and I had dreamed up. We have a Slack group where we share music and ideas with each other. I thought it would be snazzy to create a SlackBot that allows us to instantly embed an artist's top tracks into Slack. I'm also in the process of learning NodeJS, so [Hubot](https://hubot.github.com/) was the perfect place to get started. 

![slack bot in action](/assets/spotify-artist.gif)

I built a bot called Buddha-Bot, who can essentially do anything I want. This Spotify thing is just one example of a task I wrote. 
Check out my entire [Buddha-Bot project on Github](https://github.com/TheLucasMoore/buddha-bot).

## Hubot

The [documentation on Hubot](https://hubot.github.com/docs/) is fantastic. The steps to get the bot up and running are quite clear and the ability to customize the scripts with NPM is delightful. I won't rehash the docs here for you, but in summary, the bot is generated with [Yeoman](http://yeoman.io/), which scaffolds out the file structure. The setup is simple, prompting you for the information relevant to your bot. Plus, there's a sassy robot. 

![yo hubot!](/assets/yeoman.gif)

Hubot supports [so many different adapters](https://hubot.github.com/docs/adapters/) like IRC, Twitter, Yammer and even AIM. It's mind blowing. My adapter was Slack, so deploying and connecting the bot to our Slack channel was the most complicated part.

## Deploying The Bot to Slack

Honestly, [Slack's Hubot documentation](https://github.com/slackhq/hubot-slack) is pretty straightforward too. The app must be deployed to Heroku with Redis, then you have to activate the [Hubot in your Slack channel](http://my.slack.com/services/new/hubot), set up some of the config environment variables and deploy it! Read the documentation, yo. 

It's important to note that [Hubot Heroku Keep Alive](https://github.com/hubot-scripts/hubot-heroku-keepalive) requires some set up to keep the bot awake from 6am to 10pm. Free Heroku apps sleep after 30 minutes of inactivity and what's the point of a SlackBot if you need to boot it up to use it every 30 minutes? The Keep Alive script does exactly what it sounds like. 

## Functionality

So all that simply got us set up with a Node framework. It's time to write some scripts that make this bot do what we want. Within the scripts folder, we can write JavaScript (or CoffeeScript, which complies to JavaScript) to accomplish these tasks. [Read more about Hubot scripting here](https://hubot.github.com/docs/scripting/). 

The first requirement is `module.exports = (robot) ->`, which accesses the robot object, so we can create actions for the bot to hear and respond to. 

Our bot can hear, which means it always listens for keywords identified with a REGEX expression :

`robot.hear /REGEX/, (response) ->`

It can also respond, which means it listens for it's name first, then responds to your command. That uses:

`robot.respond /REGEX/, (response) ->`

I chose to use the hear command, so any mention of Spotify with an artist's name will warrant a response. The first command I built is `/spotify ARTIST`.

```javascript
robot.hear /\/spotify (.*)/i, (response) ->
    artistName = response.match[1].toLowerCase() //take the text after Spotify and downcase it
    if artistName is "creed" // ensure musical tastes are up to snuff before taxing the API
      response.send "Sorry, I only search for music."
    else
      searchName = artistName.replace(" ", "+") // format query for API request
      robot.http("https://api.spotify.com/v1/search?q=#{searchName}&type=artist")
        .get() (err, res, body) ->
          if err // check for errors
            response.send "Oh noes! #{err}"
            return
          data = JSON.parse body // parse the JSON response
          response.send "#{data.artists.items[0].external_urls.spotify}" 
          // this grabs and sends the URL of their top tracks collection
```

Note that Hubot has a [Node Scoped HTTP client](https://github.com/technoweenie/node-scoped-http-client) built right in, so making an API request is as easy as `robot.html(url)`. 

The next command I made is `jam to ARTIST`, which grabs that artist's top track only. Here it is in action:

![top tracks for an artist](/assets/beyonce.gif)

This one is a bit more complicated, as it queries the Spotify API to grab an Artist ID, then pulls that artist's top track in a second query.

```javascript
robot.hear /jam to (.*)/i, (response) ->
    artistName = response.match[1]
    searchName = artistName.replace(" ", "+")
    robot.http("https://api.spotify.com/v1/search?q=#{searchName}&type=artist")
      .get() (err, res, body) ->
        if err
          response.send "Oh noes! #{err}"
          return
        data = JSON.parse body
        id = data.artists.items[0].id // grab Artist ID
        robot.http("https://api.spotify.com/v1/artists/#{id}/top-tracks?country=US")
        // query Spotify API again with the Artist ID
          .get() (err, res, body) ->
            data = JSON.parse body
            url = data.tracks[0].external_urls.spotify
            artist_name = artistName.charAt(0).toUpperCase() + artistName.slice(1);
            track_name = data.tracks[0].name

            // the bot says the following, with the link to the top track
            response.send "#{artist_name}'s top hit is #{track_name}.\n#{url}"
```

## What's Next

This is just the start of some exciting JavaScript SlackBots. I can build almost anything with Hubot as a new tool in my tool belt. 
Some ideas of what to do next: 

1. Publish the bot to work with the [Slack Button](https://api.slack.com/docs/slack-button) for others to enjoy these creations in their channels. 
2. Since the bots can access [Process.env](https://nodejs.org/api/process.html#process_process_env) like all Node apps, I could start to do some authorized features with Spotify, like accessing a user's playlists and top artists. 
3. Adding in more commands like song-specific search, playlists, moods and anything else you can think of. 

Thanks for reading. Please let me know what you think of this project on [Twitter](https://twitter.com/thelucasmoore). 