---
title: "Comparing Frontend Approaches Part 3: Vue.js"
description: "A look at jQuery, Vue.js, React, and Elm"
date: "2017-09-19T17:49:19.797Z"
categories: []
keywords: []
---

- [Part 1: Introduction](comparing-frontend-approaches-part-1-introduction.html)
- [Part 2: jQuery](comparing-frontend-approaches-part-2-jquery.html)
- **Part 3: Vue.js**
- [Part 4: Vue.js with components](comparing-frontend-approaches-part-4-vuejs-with-components.html)
- [Part 5: React](comparing-frontend-approaches-part-5-react.html)
- [Part 6: Elm](comparing-frontend-approaches-part-6-elm.html)
- [Part 7: Final thoughts](comparing-frontend-approaches-part-7-final-thoughts.html)

![Vue.js logo](img/vuejs-wide.png)

In this part we will be implementing the [web based clone](comparing-frontend-approaches-part-1-introduction.html) of the [Mac Notes app](https://support.apple.com/kb/PH22608?locale=en_US) using [Vue.js](https://vuejs.org/). Vue.js is a JavaScript framework that came out in 2014 and has recently become one of the most popular frameworks in a very crowded market. It currently has over 65,000 stars on GitHub, which makes it the second most popular JavaScript framework (React being the first).

Vue.js uses a template based approach, which was heavily inspired by [Angular 1](https://angularjs.org/) (which was once the dominant framework). It also uses a virtual DOM under the hood, which was inspired by React. Vue.js labels itself as “the progressive JavaScript framework”, which means you don’t need to learn the entire framework to get started — you start with the basics and add on more advanced features as they become necessary. Let’s see what it’s like to get started with it in practice!

> Note — this isn’t meant to be a tutorial for people completely new to Vue.js. If you are in that category, I would recommend reading through at least the beginning of the [official guide](https://vuejs.org/v2/guide/) (which is very well written and beginner friendly) before reading through this post.

### Installation

Starting from our [initial template](https://jsfiddle.net/peterxjang/vtsjc5w9) in [part 1](comparing-frontend-approaches-part-1-introduction.html), installing Vue.js is easy enough (almost as easy as installing [jQuery](comparing-frontend-approaches-part-2-jquery.html)). Here are the two changes in the HTML head tag:

```javascript
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script src="js/notes.js" defer></script>
```

The first line includes Vue.js from a CDN. The second line includes the app’s JavaScript and only has one change from the original template — the `defer` attribute. This ensures that the `notes.js` file is loaded only after the DOM is ready. In the `notes.js` file we need the following initial code:

```javascript
var app = new Vue({
  el: "#app",
});
```

This instantiates the Vue instance — pretty much all of our JavaScript will be written and organized within this object. The `el` option has a value of `'#app'`, which is the id of the HTML div where Vue.js will bind itself to.

### Display note titles from an array of notes

Now that Vue.js is installed, let’s change the starting template to generate the dynamic notes list using JavaScript. In Vue.js, we will store any application state with an object in the `data` option. Here I’m representing the notes as an array of objects, where each object has an id, a body, and a timestamp. The `Date.now()` returns the number of milliseconds elapsed since 1 January 1970 00:00:00 UTC.

```javascript
data: {
  notes: [
    { id: 1, body: "This is a first test", timestamp: Date.now() },
    { id: 2, body: "This is a second test", timestamp: Date.now() },
    { id: 3, body: "This is a third test", timestamp: Date.now() },
  ];
}
```

Once we have the notes in the data, we can bind them to the DOM using Vue’s template syntax directly in the HTML:

```html
<div class="note-selectors">
  <div class="note-selector" v-for="note in notes">
    <p class="note-selector-title">{%raw%}{{ note.body }}{%endraw%}</p>
    <p class="note-selector-timestamp">{%raw%}{{ note.timestamp }}{%endraw%}</p>
  </div>
</div>
```

It looks like there’s only a single `.note-selector` div here, but the `v-for` directive is Vue specific syntax that will repeat the div for each note in the notes array that was defined in the Vue instance. Within any element content you can write JavaScript within double curly brackets, which is capable of referencing data defined in the Vue instance `data` option.

You can check out a live demo [here](https://jsfiddle.net/peterxjang/xjhLhs4u).

### Use computed properties and filters to sort and format notes

Similar to the jQuery example, we’ll make helper methods to make sure the notes are sorted (newest first) and formatted properly (titles should be computed from the body, timestamps should be converted from milliseconds into a human readable string). Unlike jQuery, Vue.js gives a structure to organize where such methods can go — in the `methods` option:

```javascript
var app = new Vue({
  el: "#app",
  data: {
    notes: [
      { id: 1, body: "This is a first test", timestamp: Date.now() },
      { id: 2, body: "This is a second test", timestamp: Date.now() },
      { id: 3, body: "This is a third test", timestamp: Date.now() },
    ],
  },
  methods: {
    transformNotes: function (notes) {
      return notes.slice().sort(function (a, b) {
        return b.timestamp - a.timestamp;
      });
    },
    formatTitle: function (body) {
      var maxLength = 20;
      if (body.length > maxLength) {
        return body.substring(0, maxLength - 3) + "...";
      } else if (body.length === 0) {
        return "New note";
      } else {
        return body;
      }
    },
    formatTimestamp: function (timestamp) {
      return new Date(timestamp).toUTCString();
    },
  },
});
```

To use these methods, you would change the HTML template as follows:

```html
<div class="note-selectors">
  <div class="note-selector" v-for="note in transformNotes(notes)">
    <p class="note-selector-title">{%raw%}{{ formatTitle(note.body) }}{%endraw%}</p>
    <p class="note-selector-timestamp">{%raw%}{{ formatTimestamp(note.timestamp) }}{%endraw%}</p>
  </div>
</div>
```

This works great, but Vue.js actually provides other options besides `methods` to work with data to make it both more readable and performant. One option is defining computed properties, which essentially creates new data available in the template that is computed based on existing data. In this case we can refactor the `transformNotes` function in the `methods` option into a `transformedNotes` function in the `computed` option:

```javascript
var app = new Vue({
  el: "#app",
  data: {
    notes: [
      { id: 1, body: "This is a first test", timestamp: Date.now() },
      { id: 2, body: "This is a second test", timestamp: Date.now() },
      { id: 3, body: "This is a third test", timestamp: Date.now() },
    ],
  },
  computed: {
    transformedNotes: function () {
      return this.notes.slice().sort(function (a, b) {
        return b.timestamp - a.timestamp;
      });
    },
  },
  methods: {
    formatTitle: function (body) {
      var maxLength = 20;
      if (body.length > maxLength) {
        return body.substring(0, maxLength - 3) + "...";
      } else if (body.length === 0) {
        return "New note";
      } else {
        return body;
      }
    },
    formatTimestamp: function (timestamp) {
      return new Date(timestamp).toUTCString();
    },
  },
});
```

Note the use of `this.notes` in the `transformNotes` function — any function within the Vue.js instance can refer to any value from the data object using `this`. The HTML template would refer to the computed property `transformedNotes` instead of the method `transformNotes(notes)` as follows:

```html
<div class="note-selector" v-for="note in transformedNotes"></div>
```

At a glance, using computed properties aren’t too different from using methods. The reason they’re valuable is that methods will be called whenever a re-render occurs, whereas a computed property can be cached and will only be recomputed when the underlying data changes (which Vue manages for you).

Vue.js also provides a mechanism called filters, which can be used for simple text formatting. That’s exactly what we’re doing with the `formatTitle` and `formatTimestamp` methods, so let’s see what they would look like using filters instead:

```javascript
var app = new Vue({
  el: "#app",
  data: {
    notes: [
      { id: 1, body: "This is a first test", timestamp: Date.now() },
      { id: 2, body: "This is a second test", timestamp: Date.now() },
      { id: 3, body: "This is a third test", timestamp: Date.now() },
    ],
  },
  computed: {
    transformedNotes: function () {
      return this.notes.slice().sort(function (a, b) {
        return b.timestamp - a.timestamp;
      });
    },
  },
  filters: {
    formatTitle: function (body) {
      var maxLength = 20;
      if (body.length > maxLength) {
        return body.substring(0, maxLength - 3) + "...";
      } else if (body.length === 0) {
        return "New note";
      } else {
        return body;
      }
    },
    formatTimestamp: function (timestamp) {
      return new Date(timestamp).toUTCString();
    },
  },
  methods: {},
});
```

And the HTML template would change as follows:

```html
<div class="note-selectors">
  <div class="note-selector" v-for="note in transformedNotes">
    <p class="note-selector-title">{%raw%}{{ note.body | formatTitle }}{%endraw%}</p>
    <p class="note-selector-timestamp">{%raw%}{{ note.timestamp | formatTimestamp }}{%endraw%}</p>
  </div>
</div>
```

I would say that when you’re first starting out with Vue.js, it may be helpful just to stick with methods instead of worrying about computed properties and filters, since you can pull off the same functionality. It’s nice to be aware of them as potential tools if you find yourself in need of better code organization or performance!

### Select a note on title click

Now let’s implement the ability to actually select notes. Clicking on a note title should both highlight the selected note on the left as well as display the contents in the editor on the right. Here’s a complete working example, which we’ll go over in detail:

First we need to keep track of a new variable `selectedNote` (which we’ll default to the first note). To do so, we’ll move the initialization of the data into a new `mounted` option, which is one of [several lifecycle hooks](https://vuejs.org/v2/guide/instance.html#Instance-Lifecycle-Hooks) available. The `mounted` function runs once when everything is ready in the DOM:

```javascript
data: {
  notes: [],
  selectedNote: {}
},
mounted: function() {
  this.notes = [
    {id: 1, body: "This is a first test", timestamp: Date.now()},
    {id: 2, body: "This is a second test", timestamp: Date.now()},
    {id: 3, body: "This is a third test", timestamp: Date.now()}
  ];
  this.selectedNote = this.notes[0];
},
```

Note that Vue.js requires all data that will be referred to an HTML template to be defined in the `data` object — this is why I’m defining `notes` as an empty array and `selectedNote` as an empty object. In the `mounted` function, I’m putting in the actual data. This is good organization for later, where we can replace the hard coded data with a web request from a backend for real notes data.

Now let’s take a look at the changes in the HTML template:

```html
<div class="note-selectors">
  <div
    class="note-selector"
    v-for="note in transformedNotes"
    v-on:click="selectNote(note)"
    v-bind:class="{active: note === selectedNote}"
  >
    <p class="note-selector-title">{%raw%}{{ note.body | formatTitle }}{%endraw%}</p>
    <p class="note-selector-timestamp">{%raw%}{{ note.timestamp | formatTimestamp }}{%endraw%}</p>
  </div>
</div>
```

We have two new lines — first is `v-on:click="selectNote(note)"`, which essentially works as a DOM event handler and will run the `selectNote` method when the div is clicked. We haven’t defined this method yet, we’ll look at that in a bit. The second new line is `v-bind:class="{active: note === selectedNote}"`, which will conditionally add the `active` class to the div (depending on whether or not the particular note equals the selected note from Vue’s data). It’s important to note that with Vue.js, you must use `v-bind` with any HTML attributes that you need to be dynamic.

> Side note — Vue.js provides shortcuts for `v-on` and `v-bind` — `v-on:click` becomes `@click` and `v-bind:class` becomes `:class`. I’ll avoid using the shortcuts for the sake of being explicit, but it’s good to know about them when writing and reading other Vue.js code.

The selectNote method is easy enough to define:

```javascript
methods: {
  selectNote: function(note) {
    this.selectedNote = note;
  }
}
```

At this point everything works automagically — clicking on a note will run the `selectNote` function, which updates `this.selectedNote`, which will trigger a re-render, which will bind the `active` class to the appropriate div. This is the promise of modern JavaScript frameworks — your job as a programmer is to define data and the ways it gets transformed. The framework’s job is to manage and manipulate the DOM under the hood.

The last bit here is to update the note editor on the right to display the selected note dynamically. Here’s the HTML template needed to do so:

```html
<div class="note-editor">
  <p class="note-editor-info">{%raw%}{{ selectedNote.timestamp | formatTimestamp }}{%endraw%}</p>
  <textarea class="note-editor-input">
    {%raw%}{{ selectedNote.body }}{%endraw%}
  </textarea>
</div>
```

Since the template is referring to `selectedNote`, which is defined and managed in the Vue instance, no other work is necessary!

You can check out a live demo [here](https://jsfiddle.net/peterxjang/7dep6e47).

### Edit the selected note on editor input

Next we’ll bind the `<textarea>` to the note data. Vue.js has this functionality built in with the `v-model` directive:

```html
<div class="note-editor">
  <p class="note-editor-info">{%raw%}{{ selectedNote.timestamp | formatTimestamp }}{%endraw%}</p>
  <textarea class="note-editor-input" v-model="selectedNote.body"></textarea>
</div>
```

(Note that we’re no longer putting content between the `<textarea></textarea>` tags, the Vue.js `v-model` directive is handling it now.) Now whenever the user enters text, the `selectedNote`’s body automatically gets updated! Now the only thing missing is the `selectedNote`’s timestamp — it should get updated to the current time whenever the text changes. We can accomplish this using the `watch` option:

```javascript
var app = new Vue({
  el: "#app",
  data: {
    // ...
  },
  mounted: function () {
    // ...
  },
  computed: {
    // ...
  },
  filters: {
    // ...
  },
  methods: {
    // ...
  },
  watch: {
    notes: {
      handler: function () {
        this.selectedNote.timestamp = Date.now();
      },
      deep: true,
    },
  },
});
```

This is telling Vue.js to watch the `notes` data for any changes and run the code to update the `selectedNote`’s timestamp whenever something does change. (It seems a bit strange to watch `notes` instead of `selectedNote`, but see what happens if you change it to `selectedNote` — each time the user selects a new note, its timestamp gets updated, which is not the desired behavior!)

### Create a new note with a button

Now let’s implement the ability to create a new note. Clicking on the “New” button should create a new note (new id, no body, current timestamp). The new note should become the currently selected note and appear at the top of the list of note selectors. Here’s a complete working example, which we’ll go over in detail:

There’s actually very little that’s changed here to make this feature work! In the HTML template, we need to make a `v-on:click` on the “New” button:

```html
<button class="toolbar-button" v-on:click="createNote()">New</button>
```

And in the JavaScript, we need to define the `createNote` method:

```javascript
methods: {
  selectNote: function(note) {
    this.selectedNote = note;
  },
  createNote: function() {
    var newNote = {
      id: Date.now(),
      body: "",
      timestamp: Date.now()
    };
    this.notes.push(newNote);
    this.selectedNote = newNote;
  }
},
```

(Here I’m using `Date.now()` as a quick and dirty way of generating a id, it should be replaced with a more robust approach for a guaranteed unique id.) Creating a new note is as simple as updating the `notes` and `selectedNote` data — Vue.js handles all the DOM updates necessary!

You can check out a live demo [here](https://jsfiddle.net/peterxjang/9kLqrg2n).

### Delete the selected note with a button

Again, it’s pretty easy to get the delete feature to work. In the HTML template, we need to make a `v-on:click` on the “Delete” button:

```html
<button class="toolbar-button" v-on:click="deleteNote()">Delete</button>
```

And in the JavaScript, we need to define the `deleteNote` method:

```javascript
methods: {
  selectNote: function(note) {
  this.selectedNote = note;
  },
  createNote: function() {
    var newNote = {
      id: Date.now(),
      body: "",
      timestamp: Date.now()
    };
    this.notes.push(newNote);
    this.selectedNote = newNote;
  },
  deleteNote: function(note) {
    var index = this.notes.indexOf(this.selectedNote);
    if (index !== -1) {
      this.notes.splice(index, 1);
      if (this.transformedNotes.length > 0) {
        this.selectedNote = this.transformedNotes\[0\];
      } else {
        this.selectedNote = {};
      }
    }
  }
},
```

Note that when you delete the selected note, you have to determine which note to select in its place. Finally, we need to make sure the editor itself is hidden when there are no notes. This can be done with one easy change using the `v-if` directive in the HTML template:

```html
<div class="note-editor" v-if="selectedNote.id">
  <p class="note-editor-info">{%raw%}{{ selectedNote.timestamp | formatTimestamp }}{%endraw%}</p>
  <textarea class="note-editor-input" v-model="selectedNote.body"></textarea>
</div>
```

### Filter notes on search input

The final feature I’m going to implement is to be able to search notes immediately as you type in the search input. The first step is to add a `v-model` in the HTML template on the search input:

```html
<input class="toolbar-search" type="text" placeholder="Search..." v-model="searchNoteText" />
```

Don’t forget to initialize the `searchNoteText` data:

```javascript
data: {
  notes: [],
  selectedNote: {},
  searchNoteText: ""
},
```

Now in order to get the filtering effect, we simply need to change the already existing computed property `transformedNotes` to both filter and sort (instead of just sort):

```javascript
computed: {
  transformedNotes: function() {
    return this.notes
      .filter(function(note) {
        return note.body.toLowerCase().indexOf(this.searchNoteText.toLowerCase()) !== -1;
      }.bind(this))
      .sort(function(a, b) {
        return b.timestamp - a.timestamp;
      });
  }
},
```

Perfect! The only thing missing is that filtering the list should also select the appropriate note, which means the `selectedNote` data should update whenever the `searchNoteText` data changes. We can use a watch function on `searchNoteText` for that:

```javascript
watch: {
  notes: {
    handler: function() {
      this.selectedNote.timestamp = Date.now();
    },
    deep: true
  },
  searchNoteText: function() {
    if (this.transformedNotes.length === 0) {
      this.selectedNote = {};
    } else if (this.transformedNotes.indexOf(this.selectedNote) === -1) {
      this.selectedNote = this.transformedNotes\[0\];
    }
  }
}
```

Note that I’m referring to `this.transformedNotes` in the function, since I’m trying to select the first note in the list of filtered and sorted notes.

You can check out the final working example in all its glory [here](https://jsfiddle.net/peterxjang/koza3tax/).

### Conclusion

If you compare working with Vue.js to [jQuery](comparing-frontend-approaches-part-2-jquery.html), you can see that the things you’re concerned about as a programmer are very different! In some sense I would consider jQuery to be easier to get started with, particularly if you have a good understanding of the DOM. But as we got deeper into the features, it was a lot simpler to write the Vue.js code, since we can keep our focus on data manipulation as opposed to both data manipulation plus DOM manipulation.

Learning Vue.js might seem a bit daunting to an absolute beginner — we covered the `v-for`, `v-on`, `v-bind`, `v-model`, and `v-if` HTML template directives, as well as the `data`, `mounted`, `computed`, `filters`, `methods`, and `watch` Vue instance options. However, getting started with Vue.js is a lot easier than this — you can get pretty far with with just the `v-for`, `v-model`, and `v-click` directives with the `data` and `methods` Vue instance options! Basically you should learn new directives and options as the need arises — this is what Vue.js means when it says it’s a progressive framework. And compared to the other frameworks we’ll be looking at in this series, it’s definitely the easiest to get up and running!

So the promise of Vue.js (or any modern JavaScript framework really) is that it’s a bit harder to get started, but once you learn the framework it becomes simpler to add new features. In the [next part](comparing-frontend-approaches-part-4-vuejs-with-components.html) of this series, we’ll take that concept to the next step by rebuilding the same app using Vue.js, this time with components. Stay tuned!
