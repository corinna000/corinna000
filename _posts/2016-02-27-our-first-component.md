---
layout: post
title: "Our first component"
date: 2016-02-27 12:18:00
categories: component
---
Our first component will be the search widget. 

From `MainController` we see that the search widget takes a search term bound to the form input element, uses that value to search SoundCloud (the default options tell SoundCloud to use only the poetry genre), and binds the data it finds directly to `MainController`. 

input: the search term.
output: a list of poetry tracks.

Aside from the search action in the view and passing the data along, SoundCloud itself is doing most of the work.

If we call SoundCloud directly from a unit test we'll see a side effect of SoundCloud trying to make a live HTTP call. We don't want that! To make testing easier and to promote reuse of our work we'll write a facade.

Next we'll start writing our unit test for the search component, which we'll name `so-search`.

```
Namespacing your components.

When your library of components begins to grow it won't always be obvious from which module the component you're working with comes from. There's also the possibility of creating a new component that collides with the name of another already defined.

Once while I was using a third-party UI library I defined an attribute name for a directive. Just the attribute name created a collision that took time to debug. When I looked into the source code of the offending library I found a comment indicating that invoking that directive as an attribute level directive without its namespace was a deprecated use. It wasn't depracated fast enough for me!
```

We will follow test-driven development principles where we'll write our failing test and then do the bare minimum work to get it to pass. The simplest test is to make sure the directive itself exists. Here we're looking for the `<input>` element we know our component need to work.

```
it('creates the component', function () {
      var input = element.find('input');
      expect(input.length).toEqual(1);
    });
```

This won't pass until we create our component, so let's make that now.

```
  angular.module('slamOff').component('soSearch', {
    template: '<input>'
 });
```

If we go from red to green at this point we know the following:

1. Our unit test boilerplate has been setup correctly.
2. Our component is defined and found by Angular.

Now that we know our setup is working we can proceed with testing the functionality of our component.

```

```
