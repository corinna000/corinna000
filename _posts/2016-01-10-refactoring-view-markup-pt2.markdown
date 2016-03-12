---
layout: post
title: "Refactoring view markup part 2"
date: 2016-01-10 9:38:00
categories: ng-include views
---
Although `main.html` looks a little bit better now, the longest and ugliest chunk of `main.html` is still hanging around. What's going on here? The purpose of SlamOff is to create random bracket tournaments for poetry. Most of the code in `main.html` is written to support four first-round matchups, two semi-final rounds, and a final round. 

The code for displaying the rounds, the results of previous voting choices, and even for listening to the poems is all captured using a complicated, repetitive, block of code. Business logic for showing the stages is spread between the view and the controller. Player controls for the poems are repeated fourteen times! 

Maintaining code like this is difficult. Because of the way the bracket is displayed using tables the rounds do not appear in a logical order. Voting controls for the subsequent round should only appear if the antecedent rounds are complete. In short, it's hard to look at this code and make sense from it. Although we'll place it in its own partial now, this markup needs much more attention. We'll come back to it.

    True story: While writing this demonstration application I decided to change how the voting code worked. It took me three hours to make a simple change because the code at written is so hard to modify. If it was hard for me as the original author of the code, it would be a hair-tearing experience for another developer.

For now we will create a new folder called `app/bracket` and move the HTML from `main.html` into the partial `app/bracket/bracket-partial.html` and `ng-include` it back into our main view.

    Note that when we use `<div ng-include...>` it adds another level of divs to our view template. This can cause side effects of breaking CSS where the rules are overly-specific to the structure of the code.

<!-- TODO: add a reference to good CSS standards -->

## progress bar

Our main view template is now much easier to understand.

**app/main.html**
{% highlight html linenos %}
  <div>
    <div ng-include="'app/search/search-partial.html'"></div>
    <div ng-include="'app/search/search-results-partial.html'"></div>
    <div ng-show="vm.tracks.length">
      <div>Position: {{ vm.position | date:'mm:ss' }}</div>
      <div>Duration : {{ vm.duration | date:'mm:ss' }}</div>
      <div class="progress">
        <div class="progress-bar" role="progressbar" aria-valuenow="60" aria-valuemin="0" aria-valuemax="100"
             ng-style="vm.percentPlayed">
          <span class="sr-only">{{ vm.percentPlayed }} Complete</span>
        </div>
      </div>
      <div ng-include="'app/brackets/brackets-partial.html'"></div>
      </div>
  </div>
{% endhighlight %}

The last responsibility directly coded in `main.html` is the progress bar. It looks like its related to track playing. We anticipate writing other track playing code as we continue refactoring, so let's create a folder called `track-player` and move our code into `app/track-player-partial.html`.

**app/track-player/track-progress-partial.html**
{% highlight html linenos %}
<div>Position: {{ vm.position | date:'mm:ss' }}</div>
<div>Duration : {{ vm.duration | date:'mm:ss' }}</div>
<div class="progress">
  <div class="progress-bar" role="progressbar" aria-valuenow="60" aria-valuemin="0" aria-valuemax="100"
       ng-style="vm.percentPlayed">
    <span class="sr-only">{{ vm.percentPlayed }} Complete</span>
  </div>
</div>
{% endhighlight %}

## Review

Let's look at how far we've come with `main.html`. We've gone from a view template with over 220 lines to one that easily fits on one screen. By looking at the files being included, a developer new to this application can easily see that the application has a search component, search results, a track progress bar, and the bracket display. We're off to a good start but we can do better. 

**app/main.html**
{% highlight html linenos %}
<div class="container">
  <div class="jumbotron">
    <h1><i>SlamOff</i></h1>
    <p>The Slam Poetry Bracket App</p>
  </div>
  <div>
    <div ng-include="'app/search/search-partial.html'"></div>
    <div ng-include="'app/search/search-results-partial.html'"></div>
    <div ng-show="vm.tracks.length">
      <div ng-include="'app/track-player/track-progress-partial.html'"></div>
      <div ng-include="'app/brackets/brackets-partial.html'"></div>
    </div>
  </div>
</div>
{% endhighlight %}

## Next steps

Now that our `main.html` is easier to understand we'll open up `main.controller.js` and see what's going on. We know there are opportunities ahead to move logic and dependencies into testable, reusable, better separated components. We'll be able to slim down `MainController` and make our code easier to understand.
