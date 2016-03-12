---
layout: post
title: "Wrapping Third-Party Libraries in Modules"
date: 2016-01-24 13:23:00
categories: third-party, services, modules
---
Almost all projects depend on third party dependencies. When using a module like `ui-router`, the instructions for incorporating those dependencies are straightforward.

We have two choices when we have a dependency that doesn't already have an Angular integration library. We can either call the library as a global on `window` or we can wrap that dependency in a separate angular service and include it in our code via dependency injection. 

There are a few reasons why we'd like to create a wrapper for third party libraries. First, removing a direct dependency on a third party libary gives us the ability to unit test our code without creating side effects. Second, it gives us an opportunity to understand the functionality we need from the third-party library and create abstractions that would let us more easily adapt to other libraries.

```
A real world example: I wrote an application that would help a magazine producer send marketing emails to subscribers with the headlines from that week's issues. At the time I made direct calls to the third-party library for sending test messages and approving final messages.

Last year I learned that the magazine switched to a different marketing email service. The code had to be extensively modified to use the new email provider. If I'd created adapters for interacting with the email marketing library the change would have been less painful.
```

We are directly calling SoundCloud in `MainController` for two purposes:

1. Searching for poetry on SoundCloud
2. Playing the poems in the SoundCloud players.

The wrapper we'll write for SoundCloud will grant us the following benefits:

1. We'll be able to write unit tests that don't create side effects.
2. We'll be able to use Angular's dependency injection to load the code we need rather than depending on it existing in the global scope.
3. It will separate out the SoundCloud functions, making it easier to maintain and reason about the code.
4. It will be easier to reuse SoundCloud in this or other projects.



