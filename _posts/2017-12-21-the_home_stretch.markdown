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

Upon the mounting of the app component (whenever the app component is loaded) I have set up a lifecycle event to call the dispatch function with the set session action. 

Dispatch will take an action as an argument and compare that action to its available reducers, if it finds a matching action, it will perform anything defined by that function.

In the case of set Session, that action points to another function, which will make an internal API call to /user and will then call the the LOGIN action from within the Sessions Reducer. 

The sessions reducer will define the login state and the state of a user object in the store. This data will then be available throughout other user actions. 

***Header*** 
The Header component is a stateless component which will receive user data defined by setSession as props. Based on the isLoggedIn prop, the header will either display a login button, or the user information in the header.

***Footer***
The footer component is a stateless component which will simply display a nice header with some information about the app.

***Favorites List***
This is a stateful component responsible for loading the list of images that have been favorited by the user. Within App, I have configured a route which will load this component. It's quite complicated. 

I import a number of components and libraries to support this component. Most notably, I import a library called react-photo-gallery, which displays the list of images in a nice, easily configurable grid format. This library has a number of built in methods which allow me to customize how my images can be interacted with, and it worked well for my requirements.

The first thing that I need to do is get the favorited photos that exist in my database. Similar to my setSession action, I call getFavorites on the initial page load with a componentDidMount action. I also call a componentWillReceiveProps action to make sure that my favorites load not only on the initial component load, but any time a user refreshes the page, and this will be handled by a change of props from app when setSession is called. I essentially got this from the documentation directly. When getFavorites is called, the resuting aciton makes an internal request for any favorites associated with the individual user that have been saved to the database, and returns an object with that data. Then it passes the RECEIVE_FAVORITES action to the favorites reducer which updates the state with the objects. 

I can then use the current state (thanks to mapStateToProps) to set an array of all of the favorite objects. The Gallery library expects this format. I can then pass it in, along with a customizable imageComponent object. I take advantage of this to make each image in the gallery a clickable link which triggers an individual route and renders my image detail page, defined in my routes. 

***Image Detail Component*** 

The image detail component is complex. This is a stateful component which handles the display of a single image from an external API call to instagram. 

I import a few actions that I've defined and will need to use within the component, as well as defining a couple actions internally to create favorites from this view. Similar to the Favorites list, this component needs to be stateful so that I have access to lifecycle methods to load the image both on the initial load and on refresh. 

In both cases, I call the getImage action, passing in the instagram ID. This will result in an external request to instagram for the individual image by ID, eventually updating the store with that image data, when the promises are resolved. The component will then render either that image (accessed through this.props.image.images, from the response returned by instagram), or a message explaining that the image has not been loaded. 

On the same page, the user has buttons which will call createFavorite and deleteFavorite. Clicking either of these will push through the id of the image (React Router gives us a match function which pushes through the dynamic data in the URL) and makes an internal call to save these to the database. 

This can then be accessed in the above favorites flow. 

