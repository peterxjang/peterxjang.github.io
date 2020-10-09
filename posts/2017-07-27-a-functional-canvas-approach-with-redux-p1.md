---
title: "A Functional Canvas Approach With Redux: Part 1"
description: Examining low level, object oriented, and functional drawing techniques
date: 2017-07-27
tags:
---

#### Examining low level, object oriented, and functional drawing techniques

![[peterxjang.com](http://peterxjang.com)](img/1__CnCD2uzcN4EYbg9__LLupOA.png)
_[peterxjang.com](http://peterxjang.com)_

## The Low Level HTML Canvas API

HTML Canvas provides a built in way to create 2D drawings and animations on a web page using JavaScript. The API (introduced in 2005) is a fairly low level drawing API — you can draw a rectangle, but as soon as it’s painted on the screen, it’s forgotten. Canvas doesn’t keep track of the concept of a “rectangle object” — if you want to move the rectangle, you have to erase the entire screen and draw a new one. Here’s an example of how to draw a rectangle:

```html
<!DOCTYPE html>
<head>
  <meta charset="UTF-8">
  <title>Canvas example</title>
</head>
<body>
  <canvas></canvas>
  <script>
    // Get the canvas element
    var canvas = document.querySelector('canvas');

    // Get the drawing context
    var ctx = canvas.getContext('2d');

    // Set the drawing color
    ctx.fillStyle = 'red';

    // Draw a rectangle
    ctx.fillRect(10, 10, 50, 50);

  </script>
</body>
</html>
```

In this basic example, the `<canvas>` tag defines the drawing area, and the actual drawing is done by JavaScript in the `<script>` tag underneath. Here’s the additional code necessary to “move” the rectangle by 20px to the right after 1 second passes:

```javascript
setTimeout(function () {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillRect(10 + 20, 10, 50, 50);
}, 1000);
```

To move the rectangle, Canvas requires you to first clear the whole drawing area with ctx.clearRect , then redraw the rectangle with ctx.fillRect . It’s your job to keep track of all of the variables, such as the rectangle’s x, y, width, height, and color. This gets very hard to manage when you have many objects on the screen that need to be drawn.

## A High Level Object Oriented Approach

Many people developed libraries to make it easier to use Canvas over the years — [EaselJS](http://www.createjs.com/easeljs), [Fabric.js](http://fabricjs.com/), [Paper.js](http://paperjs.org/), to name a few. All of these libraries take an object oriented approach to managing state — instead of drawing a rectangle, you create a rectangle object, which remembers its own properties. This is a completely intuitive approach, and an example where the object oriented paradigm fits well. Here’s an example of drawing and moving the same rectangle using an object oriented approach with Fabric.js:

```html
<!DOCTYPE html>
<head>
  <meta charset="UTF-8">
  <title>Canvas example</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/fabric.js/1.7.17/fabric.min.js"></script>
</head>
<body>
  <canvas id="c"></canvas>
  <script>
    // create a canvas wrapper
    var canvas = new fabric.Canvas('c');

    // create a rectangle object
    var rect = new fabric.Rect({
      left: 10,
      top: 10,
      width: 50,
      height: 50,
      fill: 'red'
    });

    // "add" rectangle onto canvas
    canvas.add(rect);

    // "Move" the rectangle
    setTimeout(function() {
      rect.set({ left: rect.left + 20 });
      canvas.renderAll();
    }, 1000);

  </script>
</body>
</html>
```

This is pretty similar to Canvas’ low level approach, but having state managed by objects is helpful, particularly when there’s lots of drawing going on. If you look at the line of code required to move the rectangle…

```javascript
rect.set({ left: rect.left + 20 });
```

…you can see that the rect remembers all its own properties — the only thing you need to modify is the left position property. Each object is responsible for keeping track of its own state, which is more organized than the low level approach.

However, there are potential downsides to an object oriented approach. Because state is encapsulated in each object, it can be difficult to see where values are coming from and how they are changing when there are multiple objects that interact with each other. This may be less of an issue when using Canvas for decorative drawings and more of an issue when using Canvas for user interfaces with complex interactions.

## A High Level Functional Approach

Instead of using an object oriented approach, we can create a higher level drawing abstraction using functional techniques. I’ll be using an approach very similar to [React](https://facebook.github.io/react/) with [Redux](http://redux.js.org/) — in a nutshell, you keep all your state in a single object, and any time the state object changes, you redraw your entire screen. Redux manages the state, React manages the view. Note that React has a “diffing” algorithm to make the redraw step as efficient as possible. I’ll be replacing React with Canvas as the view layer, which works perfectly since Canvas is designed to efficiently redraw the entire screen at each step.

So for this approach, the dependencies are Canvas (built into the browser) and Redux. In a nutshell, Redux keeps all state in a single object which contains all the necessary information to render a view. To change the state, you must dispatch an action, which is an object that describes what happened. When an action is dispatched, the state is updated using a reducer function, and the view layer (in our case Canvas) will be redrawn.

> Note that this won’t be a beginner focused tutorial on Redux — if you aren’t familiar with Redux, there are many fantastic tutorials to get you started. (I highly, highly recommend the [egghead.io videos](https://egghead.io/courses/getting-started-with-redux) by [Dan Abramov](https://twitter.com/dan_abramov), the author of Redux. He demonstrates piece by piece how to create the entire Redux library from scratch, and has a true gift for explaining things with simplicity).

Here’s how the same example of drawing and moving a rectangle looks using Canvas with Redux:

```html
<!DOCTYPE html>
<head>
  <meta charset="UTF-8">
  <title>Canvas example</title>
  <script src="https://unpkg.com/redux@latest/dist/redux.min.js"></script>
</head>
<body>
  <canvas></canvas>
  <script>
    // Get the canvas element
    var canvas = document.querySelector('canvas');

    // Get the drawing context
    var ctx = canvas.getContext('2d');

    // Define the initial state
    var initialState = {
      rectangles: [
        {
          x: 10,
          y: 10,
          width: 50,
          height: 50,
          color: 'red'
        }
      ]
    };

    // Define how state changes
    function reducer(state, action) {
      switch (action.type) {
      case 'TRANSLATE':
        return {
          rectangles: state.rectangles.map(function(rect) {
            return {
              x: rect.x + action.x,
              y: rect.y + action.y,
              width: rect.width,
              height: rect.height,
              color: rect.color
            };
          })
        };
      default:
        return state;
      }
    }

    // Define how state is viewed
    function render() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      var state = store.getState();
      state.rectangles.forEach(function(rect) {
        ctx.fillStyle = rect.color;
        ctx.fillRect(rect.x, rect.y, rect.width, rect.height);
      });
    }

    // Create the Redux store
    // (to manage state changes)
    var store = Redux.createStore(reducer, initialState);
    store.subscribe(function() {
      render();
    });

    // Draw the initial view
    render();

    // "Move" the rectangle
    setTimeout(function() {
      store.dispatch({type: 'TRANSLATE', x: 20, y: 0});
    }, 1000);

</script>
</body>
</html>
```

This functional approach is a lot more code than the low level and object oriented approaches! But this is the up-front cost — it’s harder to set up in the beginning because we’re creating a rigid structure for every new piece of code to live. As we add more features, there ends up being considerably **less** code compared to the low level and object oriented approaches.

I used this approach to make my personal homepage ([peterxjang.com](http://peterxjang.com)), where the entire interface is a Canvas surface with panning/zooming interactions (using either a mouse or touch interface). The only external dependency used is Redux (2kB). I was able to get the entire HTML/CSS/JS to fit under 14 kB, which means it renders the initial paint blazingly fast (a single round trip), even on slower mobile connections.

> Some secret features on my [homepage](http://peterxjang.com) — you can double click the background or parent items to zoom-to-fit. You can long press any item, which allows you to move and resize each item. You can also long press the background, which gives you options to save your layout changes (in local storage). Again, all these interactions were coded with a total initial page size under 14kB (I implemented image and content lazy loading to keep the first paint as fast as possible).

Not only is the code light weight, but it’s easy to reason about. All the application state is contained in a single state object, which means anyone can look at that single object to understand the data that affects the view. The only way for the state to change is by dispatching actions, which forces you to define all interactions in terms of action objects. Anyone can look at all the actions defined to understand the complete set of ways you can interact with the application. Instead of encapsulating state and behavior in an object oriented approach, everything is laid bare in a functional approach.

In the next part of the series, we’ll continue using using this functional approach to implement user interactions (panning and zooming).

[Part 2: Using the 3 fundamental principles of Redux](a-functional-canvas-approach-with-redux-part-2.html)
