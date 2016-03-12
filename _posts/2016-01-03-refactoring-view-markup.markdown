---
layout: post
title: "Refactoring view markup part 1"
date: 2016-01-03 12:18:00
categories: ng-include views
---
We refactor our code to make it easier to reason about how the code works. When we find a bug we need to fix, or when there's a new feature to add, it's easier to do our work if we can make sense of what we're seeing.

As we look at [SlamOff](https://github.com/corinna000/refactoring-angular/tree/before_refactoring) before refactoring, we see that most of the application lives within `main.html` and `main.controller.js`. There are too many responsibilities crammed into too few units of work. We're going to start by looking at `main.html` to work out its responsibilities, and then we'll use [ng-include](https://docs.angularjs.org/api/ng/directive/ngInclude) to start creating partials that are easier for us to reason about.

**The Refactoring Mantra**

    Refactoring is a disciplined habit of improving the internal structure of software to make it easier to reason about its operation. Refactoring is a practice of software development, not a goal unto itself.

Keeping our refactoring mantra in mind, let's look at `main.html`. We know this view is doing a lot of different work. To help us start to reason about its responsibilities we'll begin by moving partials of HTML into separate files and then pulling them into the view using `ng-include`.

This is only a starting point to help us understand the responsibilities of our main view. After another pass of refactoring most of these partials will probably be moved into separate components. 

Let's take a closer look at `main.html` and identify some responsibilities

* Search for poetry
* Display a list of search results
* Choose a subset of the search results for the SlamOff
* Display the poetry bracket
* Vote on each item of the poetry bracket
* Play a poem
* Display the track position of the playing poem
* Show the search results

Whew! Those are a lot of responsibilities. Each one of those could be converted into a component. In fact, for the items that are repeated (the bracket sections, the play controls for each poems), converting those items to components would add reliability and reduce complexity of `main.html`. 

There's one more responsibility `main.html` has that isn't in our list. Do you see it? It's the responsibility of tying all our functionality together into the shell of the application. Even if we converted everything on our list into a component we'd still need a way to tie all of that functionality together. 

## search panel partial

Let's begin by moving the search panel into a partial. The search panel is helpfully contained within a div marked as a panel. We'll move this entire div into its own file and then include it into `main.html`. We will also create a folder called `app/search`. This helps organize our application by functionality.

**app/search/search-partial.html**
{% highlight html linenos %}
<div class="panel panel-default">
  <div class="panel-body">
    <form class="form-inline" ng-submit="vm.searchForPoetry()">
      <div class="form-group">
        <label for="poetrySearch">Search for Slam Poetry</label>
        <input class="form-control" id="poetrySearch" name="poetrySearch" ng-model="vm.model.poetrySearch">
        <button class="btn btn-primary" type="submit">Search</button>
      </div>
    </form>
  </div>
</div>
{% endhighlight %}

To include the new partial into `main.html` we'll replace our old markup with the following code:

**app/main.html**
{% highlight html linenos %}
  <div ng-include="'app/search/search-partial.html'"></div>
{% endhighlight %}

Note: The `ng-include` directive takes an expression as its value, therefore we need to add single quotes around the path to our partial. This is easy to overlook, and you wouldn't be the first person to make this mistake.

Let's take a closer look at our partial, in particular the variables it uses from `MainController`.

* vm.searchForPoetry()
* vm.model.poetrySearch
 
We can start to make some inferences about a future search component. This component takes user input and searches SoundCloud for matches. Although not explictly shown in this partial, the search will end with search results. We can see this in our next fragment: 

**app/search/search-results-partial.html**

{% highlight html linenos %}
<div id="search-results">
  <div ng-show="!vm.selectedTracks.length"></div>
  <div ng-show="vm.selectedTracks.length > 0 && vm.selectedTracks.length < 8">Select <span
    ng-bind="8 - vm.selectedTracks.length"></span> more tracks</span></div>
  <div ng-show="vm.selectedTracks.length == 8">
    <button class="btn btn-primary" ng-click="vm.startSlamOff()">Start SlamOff!</button>
  </div>
  <div ng-show="vm.selectedTracks.length > 8">You selected too many tracks!</span></div>
  <form name="checkedItems">
    <ul>
      <li ng-repeat="track in vm.searchResults track by $index">
        <label>
          <input type="checkbox" value="{{ $index }}" name="track_{{$index}}" ng-model="track.selected">
          {{ track.title }} ({{ track.duration | date:'mm:ss' }})
        </label>
      </li>
    </ul>
  </form>
</div>
{% endhighlight %}

After moving html into a partial, we'll include it in `main.html`. We've reduced the markup in `main.html` by about thirty lines. It's now a little easier to reason about the organization and responsibility of `main.html`. 
**app/main.html**
{% highlight html linenos %}
<div ng-include="'app/search/search-results-partial.html'"></div>
{% endhighlight %}

## Refactoring so far

* In order to make `main.html` easier to read and reason about we moved blocks of HTML into separate files and used `ng-include` to pull the HTML back into our main view. 

* We created a folder for our partials called `search` that will help us keep our code organized by feature type. Later on we will create search components and the code for those will be in these folders.

In the next section we'll continue moving HTML from `main.html` into separate partials. 
