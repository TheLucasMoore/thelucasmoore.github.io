---
layout: post
title: What to consider when building an API
author: Lucas Moore
---

Digging into APIs is one of the reasons I'm [learning to code](http://thelucasmoore.com/2016/05/15/Why-Im-Learning-To-Develop-Software.html). Application Programming Interfaces (APIs) and a crucial part of the deep structure of the web. There's so much data accessible for developers to play with. It's a ton of fun. After building a few projects that incorporate APIs, I've got some ideas about how to best build them.

## What to Consider

When building an API for my latest project, I kept three main factors in mind. Simplicity, comprehensiveness and security. 

# Simplicity

A simple API is beautiful thing. When building my latest application's API, I shared only the data I wanted to be rendered on my front end. This was an application that tracked both artists and genres, so I considered whether to always render the full nested data structure. In the end, I decided to keep it the most simple. This is to help developers easily sort through the data, even as the scale grows larger. Simple is always best.

Keep the level of nested attributes below three levels. If you need to go deeper than that, reconsider the structure and split the data up into separate endpoints. 

# Comprehensiveness

It's also crucial to balance simplicity with comprehensiveness. Thinking of what data developers might play with is a large part of why APIs even exist. I was disappointed to dig into the Spotify API recently and realize that the total number of streams, artist biographies and amount of plays users have of each track are unavailable. I had to work around this for my [final project](http://dev.thelucasmoore.com/2016/06/05/The-Spotify-API-and-Angular.html), but I could have just made it work more easily if the API was more comprehensive in its dataset.

# Security

The last major consideration for building an API is security. Does the API expose any user data like emails, passwords (gosh I hope not), any [API tokens](https://labs.detectify.com/2016/04/28/slack-bot-token-leakage-exposing-business-critical-information/) or Oauth refresh tokens? That's obviously a big no-no, but it does happen, as you can read in the link above.

## What not to do

When playing around with the Last.fm API in a recent project, I encountered one of the JSON data keys that began with a hashtag. This was frustrating. The character interferes with normal javascript syntax and I couldn't access the data without some complicated work arounds. This could be avoided if they'd stuck to formal object naming conventions of using strings and integers. 