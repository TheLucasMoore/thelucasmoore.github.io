---
layout: post
title: AWS Lambda and NodeJS Slack bot
author: Lucas Moore
---

I've wrapped up a neat project using [AWS Lambda](https://aws.amazon.com/lambda/) (with [Serverless](https://serverless.com/) behind the scenes) to improve the musical [Mew Slack bot](http://mewsic.herokuapp.com/).

I wrote about this project when I [first made it](http://dev.thelucasmoore.com/2016/07/11/Building-Slack-Bots-with-Node.html) months ago.
Now I've gone back and improved the h*ck out of it.

![mewsic](/assets/mewsic.gif)

# The Problems

### Response Time
Slack requests need a response within 3 seconds or they'll time out and kick an error to the user.
The old Node/Express endpoints were hosted on a free Heroku dyno, so as soon as a request was made, it would attempt to wake the dyno, which would obviously time out. So the integration didn't work on the first call, but worked on the second, once the Heroku app was awake. No bueno.

### Content Errors
Some slash commands used two API calls to get their information. Requesting an artist's bio and top tracks happened separately. This caused issues where the data from Last.fm would return one artist and Spotify would return another artist.

# The Solution

![lambda](/assets/lambda.jpg)

## Go Serverless
Rethinking the infrastructure, AWS Lambda is the perfect option to deliver instant responses. It is a code-only, serverless solution. Request information, the Lambda spins up, kicks out a response and then spins back down. There's a free tier of a million requests a month. Afterwards, it's [insanely cheap](https://aws.amazon.com/lambda/pricing/). That solves our response time issue.

We use the Serverless framework at Ibotta, so I learned it. Check out [the docs](https://serverless.com/framework/docs/), which are super straight forward.

## Use Promises
For the mismatched data issue, I sat down and properly learned how to use [Node's Promises and callbacks](https://github.com/maxogden/art-of-node#callbacks) for async requests. I wanted the fewest dependencies possible, so I followed [this guide](https://www.tomas-dvorak.cz/posts/nodejs-request-without-dependencies/) to writing a Promise function myself.

## What I learned

* Lambas don't have a great system for deploying. Serverless fixes that. Using `serverless deploy` will zip up the files, send them to S3, create your lambdas and then put API Gateway endpoints in front of them. Snazzy stuff. I then pointed the Slack app to the production end points from API Gateway.

* Slack requests come encoded in `x-www-form-urlencoded` format. We need it to be JSON so we can pull out necessary information for the API requests the bot makes. I followed part of [this blog post by Ryan Ray](http://www.ryanray.me/serverless-slack-integrations) to do a data transformation within API gateway.

* Testing is crucial. I used [lambda-tester](https://www.npmjs.com/package/lambda-tester) to create specs around each endpoint.
I was testing from Postman for a while. Making changes, redeploying and then testing again isn't the best workflow. I'm obviously keeping the API keys secret in `process.env`, which means you can't run the specs locally if you cloned it.

# Try it out!

* [Check out the code](https://github.com/TheLucasMoore/mewsic_aws_lambda)
on my personal Github page.

* [Install the Slack Bot](http://mewsic.herokuapp.com/) on your Slack channel already!

* [Let me know](https://twitter.com/thelucasmoore) what you think.

* <3 Lucas
