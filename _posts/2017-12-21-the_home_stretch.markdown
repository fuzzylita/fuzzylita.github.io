---
layout: post
title:      "The home stretch"
date:       2017-12-21 15:33:45 -0500
permalink:  the_home_stretch
---


The React Project for our final assessment was deceptively light on requirements, at least on first glance. Once I got into the weeds however, the project was quick to spiral into something larger than I'd expected. I will walk through how I addressed each section here:

**Setting up a Rails API to persist data: **

Rails 5.1 now supports webpack natively, and therefore React. I found several articles which walked through how to configure Rails in this way. As this seemed to be the most current way of managing integrating the React frontend with a Rails backend, I followed suit and set it up.

In my Application controller, I built out standard helper methods for the user status, in the form of current user, isLoggedIn and require login. Once set up, I thought about what information I would need to persist to my database. I knew I'd need data for the following: 

1. The sessions controller to support OAuth for instagram, and create a new session
2. A user controller to create a user and surface it in my frontend
3. A favorites controller to save images from instagram that the user favorites

***Building out React***

The React app was more complicated than I'd expected. I will speak on the main components first and then break down the flow of data for each component. 

Index.jsx is responsible for importing key methods from redux, setting up my store, and combining my reducers so that I can update my state based on any of my actions. I import thunk so that I can use functions in my actions, in order to make external requests. Finally this makes the store available to my other components through Provider, and mounts my App component.

The App component is responsible for rendering all of the other components based on the route. I installed react router and imported the various components that I'd need to use for react router, as well as importing connect, so that I could use mapStateToProps. I also elected to show notifications when a user saves a photo, so I installed a library called react-toastify which makes this extremely easy.

Within this component, I immediately set the session upon load by using dispatch. Finally, the core of the app is rendered based on the components that will be loaded by my render function. This will change repeatedly throughout the user experience as the props and state change. I'll now talk about the flow of data through each component.

***Router***

React Router is built into react, and once installed, allows me to define which specific components are loaded based on the URL that the user enters. Components to be loaded are defined under each <ROUTE > tag, but I also included switch and redirect, part of the react router library.

Switch will wrap the routes, and will behave essentially like a switch statement. The first route that matches will render it's component. I also added redirect, as a catchall for circumstances where a user enters an undefined route.

***componentDidMount > setSession***

Upon the mounting of the app component (whenever the app component is loaded) I have set up a lifecycle event to call the dispatch function with the set session action. Dispatch will take an action as an argument and match that action to its available reducers, and perform anything defined by that function. In the case of set Session, that action points to another function, which will make an internal API call to /user and will then define a user object in the store. 
