---
layout: post
title: How to Publish Slack App Integrations
author: Lucas Moore
---
I got interested in [building Slack Bots](http://dev.thelucasmoore.com/2016/07/11/Building-Slack-Bots-with-Node.html) after discovering the [Hubot](https://github.com/github/hubot) framework a few weeks ago. It was simple to start building an integration for my own Slack channel using a little CoffeeScript with Hubot.

I decided I wanted to build and publish a bot to integrate with other folk's channels, which started to get complex quite quickly. After a few days of learning Node and Express, as well as the ins and outs of a few APIs, Mewsic was finally born!

![mewsic](/assets/mewsic.gif)
(it's a mew-based music bot... get it?)

<a href="https://mewsic.herokuapp.com" target="_blank">Mewsic</a> is a bot that instantly pulls information from four different music APIs. You can read about all that on the <a href="https://mewsic.herokuapp.com" target="_blank">Mewsic Heroku Page</a>, so I won't repeat myself here. I ventured into undocumented territory that had no blog posts or API documentation to rely on. So, for your benefit, here's what I learned. There's a video at the bottom of the post showing the installation and use of Mewsic, too.

## Node + Express == Best Friends

In school, I learned the Rails + JavaScript stack. I absolutely love JavaScript's power, so delving into an all-JavaScript stack was lots of fun. No CoffeeScript to rely on either, just pure ol' JS. Node has a lot of great features, like package management with NPM. Installing and using Express was a relatively easy learning curve.

Express runs a minimal server and allows for GET and POST requests to handled right in the file. Por ejemplo:

```javascript
var express = require('express');
var app = express();

app.use('/', express.static('www')); // snazzy static homepage

app.get('/success', function(req, res){
  res.sendFile('success.html', { root: path.join(__dirname, '/www') });
  // sends a file for one redirect page
})

app.post('/artist', function(req, res){
  // code to handle the post request
}
```

This is like a hybrid of <code>Routes.rb</code> and a controller in a Rails application, all in one file.

## Slack Commands Need Web Hooks and Responses

Since Slack's [custom slash commands](https://api.slack.com/slash-commands) rely on incoming web-hooks, which are sent as POST requests, I set up POST routes to handle each command. The text coming after the command can be isolated with `req.body.text`, which I then parsed and sent to whichever API fits in with that command. To send the response back to Slack, I built a body of the response data and sent it out with `res.send(body)`

```javascript
// within each app.post('/foo')
body = {
  "response_type": "in_channel",
  "text": albumLink,
  "attachments": [
  {
    "title": albumName,
    "image_url": albumArt
  }]
  };
res.send(body);
```

## Error Handling is Important

![heroku errors](/assets/app-error.png)

Heroku apps can be fragile. If you hit an error because some variable is undefined, the whole app crashes. This can happen if a user doesn't spell an artist's name correctly, meaning the JSON data that is returned is empty, leaving nothing for the rest of the script to parse. I handled these errors by checking the length of whichever dataset should contain the albums, artists, etc.

```javascript
request(url, function (error, response, body) {
    var data = JSON.parse(body);
    if (data.albums.items.length > 0) {
      var albumName = data.albums.items[0].name
      // etc...
    } else {
      res.send(errorBody)
    }
}
```

## Authorizing Slack Apps
![slack](/assets/slack.png)

The most gratifying part of this project was getting the legendary Slack button to work. It works seamlessly, so you'll never know the work I put into this step.

Slack uses [Oauth2 verification](https://api.slack.com/docs/oauth), meaning I request access at a certain scope and a user approves it. Slack then sends me a token, which I can exchange for access to that verified channel. I felt the documentation was a bit unclear here. What I needed to do was to send a POST request to `https://slack.com/api/oauth.access` structured like this:

```javascript
var data = {
  form: { // store your sensitive info in ENV, of course
    client_id: process.env.SLACK_CLIENT,
    client_secret: process.env.SLACK_SECRET,
    code: *THE TOKEN*
  }
}
```
And magically, you've now have installed my app in your Slack channel!

## Most importantly: HAVE FUN

![pokemon](/assets/pokemon.png)

Building things should be downright fun. Broken apps can feel frustrating, but I find the satisfaction of building a working app worth a week of struggle. Writing the HTML and CSS for this project was a blast from the past. I designed the background myself in Photoshop and used an 8-bit Google Font called [VT323](https://www.google.com/fonts/specimen/VT323). The images are pulled from Pokemon Red screenshots, like the one above, which I grew up playing.

I'm most proud of the borders around each section, which is some CSS magic with border-radius, background-clip and border-image.

```css
section {
  background-color: #fff;
  background-clip: padding-box;
  border-radius: 20px;
  border: 10px solid transparent;
  padding: 20px;
  -webkit-border-image: url(images/box-border.png) 60 round; /* Safari 3.1-5 */
  -o-border-image: url(images/box-border.png) 60 round ; /* Opera 11-12.1 */
  border-image: url(images/box-border.png) 60 round;
}
```
Here's the bot in action. You can install it on your own Slack channel in three clicks. The site is <a href="https://mewsic.herokuapp.com" target="_blank">right here.</a>
<iframe width="420" height="315" src="https://www.youtube.com/embed/IQsEUbiBVV8" frameborder="0" allowfullscreen></iframe>
