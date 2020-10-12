---
title: "Comparing Frontend Approaches Part 1: Introduction"
description: "A look at jQuery, Vue.js, React, and Elm"
date: 2017-09-15
categories: []
keywords: []
---

- **Part 1: Introduction**
- [Part 2: jQuery](comparing-frontend-approaches-part-2-jquery.html)
- [Part 3: Vue.js](comparing-frontend-approaches-part-3-vuejs.html)
- [Part 4: Vue.js with components](comparing-frontend-approaches-part-4-vuejs-with-components.html)
- [Part 5: React](comparing-frontend-approaches-part-5-react.html)
- [Part 6: Elm](comparing-frontend-approaches-part-6-elm.html)
- [Part 7: Final thoughts](comparing-frontend-approaches-part-7-final-thoughts.html)

In the book [_The Pragmatic Programmer_](http://pragprog.com/the-pragmatic-programmer "The Pragmatic Programmer"), David Thomas and Andrew Hunt make the following recommendation:

> Learn at least one new language every year. Different languages solve the same problems in different ways. By learning several different approaches, you can help broaden your thinking and avoid getting struck in a rut.

While I don’t think that advice applies directly to learning a new frontend framework each year, it’s still worthwhile to try to understand the fundamental approaches different frameworks are taking to solve the same problem. We’ve recently seen tremendous progress in the frontend space due to frameworks influencing each other, from React’s virtual DOM, Ember’s CLI, and The Elm Architecture, just to name a few.

So to understand the ways different frontend approaches solve the same problem, I’m going to be walking through building the same app with 5 different libraries and frameworks. The application I will be making is a simple web-based clone of the default [Mac Notes app](https://support.apple.com/kb/PH22608?locale=en_US), which is a nice target for a couple of reasons:

- The app already exists, so the features are fully specified
- The app is pretty simple in scope, so it’s not too time consuming to build
- The app has some nice reactive features, which requires non-trivial JavaScript

(This is similar to the [TodoMVC](http://todomvc.com/) project, which implements a basic todo app with different frameworks. The notes app is a bit more dynamic, but still pretty similar in scope. The key difference is that I’m going to be explaining every step as I build each app from scratch in the same order, so you can understand each approach without having to learn the entire framework up front).

Here is a quick look at what the desktop Mac Notes app looks like to give you a sense of some of the reactive features. Note how the app is reacting immediately in various places as you type:

![Mac notes app](/img/1__Za9oW5CKgqdBmbt315Mkjg.gif)

I generally implement the following features in my web-based clone of the Mac Notes app in this order:

- **Store notes as an array of objects** (a note has an id, body, & timestamp)
- **Display note titles on the left**, sorted with newest on top (note titles are computed based on the note body)
- **Select a note on title click**, which highlights the selected note on the left and displays the selected note body on the right
- **Edit the selected note on input**, which automatically updates the title and timestamp on the left
- **Create a new note with a button**, with an empty body and current timestamp
- **Delete the selected note with a button**, which updates the left notes list
- **Filter notes on search input**, which automatically updates the list of notes as you type each character

The clone app starts off with the same HTML and CSS template (no JavaScript yet), which you can see below (click on the Result tab):

<script async src="//jsfiddle.net/peterxjang/vtsjc5w9/embed/result,html,css/"></script>

Some notes about this starting template:

- There is some placeholder HTML content which will be replaced with dynamically generated JavaScript content.
- I added a lot of classes to the HTML to make it easier to style and manipulate the DOM. Most JavaScript frameworks don’t utilize direct DOM manipulation, but it’s there just in case.
- I’m using CSS flexbox for the application layout. I’m not a flexbox guru (pretty much learned what I needed from [Flexbox Froggy](http://flexboxfroggy.com/) and [Solved by Flexbox](https://philipwalton.github.io/solved-by-flexbox/)), so there may be a more graceful way to handle the CSS layout!

In this series I will be building the same notes app with the following frontend approaches — [jQuery](comparing-frontend-approaches-part-2-jquery.html), [Vue.js](comparing-frontend-approaches-part-3-vuejs.html), [Vue.js with components](comparing-frontend-approaches-part-4-vuejs-with-components.html), [React](comparing-frontend-approaches-part-5-react.html), and [Elm](comparing-frontend-approaches-part-6-elm.html). This was chosen roughly in order of easier to learn but complex to maintain (jQuery) to simple to maintain but harder to learn (Elm). Stay tuned!
