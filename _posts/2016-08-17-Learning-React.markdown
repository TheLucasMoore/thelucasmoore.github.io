---
layout: post
title: Learning React. Thinking in React.
author: Lucas Moore
---

![React Logo](https://blog.dashlane.com/wp-content/uploads/2016/02/react-logo-2.png)

My background in education has given me a lot of experience in [learning how to learn](https://medium.com/learn-love-code/the-neuroscience-of-learning-how-i-learned-to-learn-at-learn-verified-2001c79598ea#.simjcs7q0). Component based front end frameworks are all the rage and I’ve worked with Angular before. So React should be easy to learn, right? Yeah, sorta. There have been some gotchas as I’ve started to build things with React. After spending a week learning React, I can see its strengths and have felt some of the framework's frustrations.

## Why React? The Strengths.

React is popular for a reason. Facebook developed it to solve their own unique issues of scale. It’s designed as a dynamic front end framework that seamlessly detects and updates components. It can react to any change by automatically running a difference between the DOM and incoming data.

A component can be initialized with certain data and then it automatically updates to reflect server-side changes.

```javascript
getInitialState() // sets an initial state of a component

compnentWillMount() // as soon as the component is mounted, do things like $.getJSON
```

[GraphQL](http://graphql.org/docs/getting-started/) is a game changer. Instead of dealing with this constant back and forth of sending and receiving data from backend to front end, what if the components themselves understood the schema of the JSON data they needed and could grab it? Like a template on a component. Yeah, great idea! That’s what GraphQL can do.

Another great thing about React is it’s always being built. It’s a young framework with a new and exciting gizmo out almost each week. This creates more of a learning curve (more on that right below) but the upside is that there is a strong community using React, building for React and discussing the best practices.

## Ugh. React. The Struggles.

For my first React project, I’m building a polling application. It uses Rails as an API backend and Node/React for the front end. These two separate servers running and talking with each other is powerful, but lead to some confusion at first. Back end and Front end are their own little worlds and the changes I may need to update my app have become doubled.

This young framework also has constant updates. Any minor update shouldn’t cause breaking changes, but major updates do. A frustrating series of events of React updates went as follows.

- I got an error saying [Uncaught TypeError: Super expression must either be null or a function, not undefined](http://stackoverflow.com/questions/30116430/reactjs-giving-error-uncaught-typeerror-super-expression-must-either-be-null-or), which meant I needed to update to React 0.13.
- This caused a new issue with [React Router](https://github.com/reactjs/react-router/issues/638)
- Which meant I needed to make [a few updates](https://github.com/reactjs/react-router/blob/832c42946c874fe56ffde0066b1088054311cb98/CHANGES.md) just to return to where I was a day ago.

## The Takeaway

Learning a new framework is adopting a new mindset. The first phase is comparing the new stuff to what you already know, like learning a spoken language, it is routed through the native tongue. Then the next stage is starting to natively speak in React. Last night I woke up literally dreaming of a super clean Rails API feeding into a set of React components. Though I feel stuck on the little things like new errors, I feel good that my mind is starting to think in React.
