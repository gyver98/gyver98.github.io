---
layout:     post
title:      React Music Application :Part1
date:       2017-02-01 12:31:19
summary:    Hello React! Create a simple React music player application part1 
categories: blog development react
---

Create React application Part1
==================

This is an article that I've decided to run a blog to organize and share what I have learned and write it for the first time.
In this article, I would like to share how I made an simple application to look up music album with Spotify API based on the things I learned while studying React, and the various materials referenced on the internet.


React?
-------------

React is a JavaScript library for user interface developed on Facebook.
In Component-based architecture React, a component that is an important building block is an independent, reusable piece that makes up the UI, and conceptually, the components in React are tha functions of JavaScript.
The component accepts inputs called `Props` and returns `React elements` describing what should be shown on the screen. 

This idea can be expressed in one word as the following formula.

> fn(d) = V.

A function that accepts data as input and returns a view.

Create React App
-------------
`Create React App`[^1] is a new tool for React application development, which allows you to start a React project directly without complicated setup.
The following commands make it easy to install and start your application immadiately.

>- npm install -g create-react-Application
>- create-react-app my-app
>- cd my-app/
>- npm start

After creating a new application through `Create React App`, open `http://localhost:3000/` and check the application created.
If you see the screen below, the project has been successfully set up.

![](https://lh3.googleusercontent.com/EeKbF6zn5lnouSSdulf4uvRPqxxCEo75P-shtCF5Fh4aOb3A3Xsu7sbSsiitLfd-UggKlz3D5Q=s944 "Screen Shot 2017-02-01 at 3.21.15 pm.png")

Breaking Down the UI
-------------
Almost all React application UIs consist of a <span class="bg-dark-gray white">combination of components</span>.
For example, a weather app consists of a component that shows the name of the region, a component that shows the current temperature, and a graph component that shows the forecast for five days.

For this reason, it is a good practice to decompose the UI into component units before developing the React app.

> See [Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html) for an approach to viewing applications as a combination of components.

The layout of this application is shown below.
![](https://lh3.googleusercontent.com/ycAd5tgymot5yzEQ4s2fJP4-Om3z73NYEmRcsvt9JsaMYcLlMl4DCaqtCw8dbB1PFO4YdLFCLA=s944 "music_search")

The UI is decomposed into component units as follows.
![](https://lh3.googleusercontent.com/JEYvOdX3ww3CAahxq4k3v1XBfA9h_4UXY9NINOrUlJgKL_pOfNORZN1WL9fBOjrFB_-TDu_A5w=s944 "Screen Shot 2017-02-01 at 2.11.14 pm.png")

There are two things to notice.

1. Instances of individual Album components belong to the AlbumList component. Normally, we say component B is nested in component A when we see component B inside component A. Or component B is a child of component A. (Parent - Child relationship)
2. There are actually four components here. The fourth component is the App component that acts as a container for the entire app. It's a good practice to have the topmost component that serves as the UI entry point, initializing the state of the app, and controlling how the child components should be organized.


The App Container
-------------
Before we get started, let's put together the project source structure.
We will leave only the files we need for the project and delete the rest.
(deleted `App.test.js` , `logo.svg`)

Now our src directory should look like this:

```
src
 - App.css
 - App.js
 - index.css
 - index.js
```

[^1]: [Create React apps with no build configuration. - GitHub](https://github.com/facebookincubator/create-react-app)

{% include disqus.html %}
