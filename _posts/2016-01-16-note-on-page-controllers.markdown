---
layout: post
title: "A note on page controllers in SPAs"
date: 2016-01-16 13:23:00
categories: controller
---
The *page controller* pattern is commonly seen in server generated multi-page web applications (MPAs). Within a framework like ASP.NET, the page controller is tasked with handling HTTP requests and updating models and views appropriatly. 

Single page applications (SPAs) are architected differently from server-side web apps. The user's interactions within an SPA are handled within the client itself and calls to the server are only necessary to retrieve or update data. Unlike an MPA, there is no need for an SPA to generate new pages as the user navigates through the application. The initial load of the application (eg, the "single page") is the only time the application needs to be bootstrapped and loaded within the client's browser. 

What I've noticed while working on SPA projects is that the paradigm of the MPA is difficult to escape. Features of the SPA are specified by "pages" or "screens". This biases the design of the application against modular, reusable libraries and components and more towards ["big balls of mud"](https://en.wikipedia.org/wiki/Big_ball_of_mud) where application responsibilities such as form validation, user authentication, and error handling are duplicated for each section of the application. 

The routing libraries `ui-router` `ng-route` particularly encourage the page controller pattern by making it easy to relate a controller to a given route. Ideally, route controllers would have responsibilities limited to responding to changes in the route or handling route parameters. In practice, route controllers become defacto page controllers containing all the business logic associated with the components appearing at a given route.

Framework developers are deliberately moving away from the page controller pattern for singe page applications. In Angular 2 standalone controllers have been removed and replaced with [components](http://learnangular2.com/components/). EmberJS is also removing controllers in favor of components. 

We will not be removing controllers from the *SlamOff* application because we're going to continue using Angular.js, but we are going to start moving code out of `MainController` into discrete, reusable components. Not only will this make our code simpler to maintain, but it will place us in a good position to upgrade our components to Angular 2.

