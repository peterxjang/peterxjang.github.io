---
title: "Comparing Frontend Approaches Part 2: jQuery"
description: "A look at jQuery, Vue.js, React, and Elm"
date: 2017-09-18
categories: []
keywords: []
---

- [Part 1: Introduction](comparing-frontend-approaches-part-1-introduction.html)
- **Part 2: jQuery**
- [Part 3: Vue.js](comparing-frontend-approaches-part-3-vuejs.html)
- [Part 4: Vue.js with components](comparing-frontend-approaches-part-4-vuejs-with-components.html)
- [Part 5: React](comparing-frontend-approaches-part-5-react.html)
- [Part 6: Elm](comparing-frontend-approaches-part-6-elm.html)
- [Part 7: Final thoughts](comparing-frontend-approaches-part-7-final-thoughts.html)

![jQuery logo](img/1__7aM1iGhORpH__edtcYbtQsA.png)

In this part we will be implementing the [web based clone](comparing-frontend-approaches-part-1-introduction.html) of the [Mac Notes app](https://support.apple.com/kb/PH22608?locale=en_US) using [jQuery](https://jquery.com/). jQuery is a JavaScript library that came out in 2006, and within several years became the most popular JavaScript library in use on the web (used by [70% of the top 100,000](http://trends.builtwith.com/javascript/jQuery) sites online).

jQuery provides an intuitive API to directly manipulate the DOM to create interactive websites. In 2017, jQuery’s DOM manipulation approach is far less dominant, with templating and virtual DOM based approaches taking over. Nevertheless, jQuery remains an excellent library that’s reasonably lightweight, easy to get started with, and still used by many apps in production today. Let’s see what it’s like to use in practice!

> Note — one could argue that it would make more sense to start with vanilla JavaScript instead of jQuery, which is completely reasonable. jQuery provides some concise helper functions that makes it easier to demonstrate certain features, but you should feel free to mentally substitute any jQuery specific approaches I’m taking with the corresponding vanilla JavaScript — the general approach should be the same.

### Installation

Starting from our [initial template](https://jsfiddle.net/peterxjang/vtsjc5w9) in [part 1](comparing-frontend-approaches-part-1-introduction.html), installing jQuery requires only 2 lines in the HTML head tag:

```html
<script
  src="https://code.jquery.com/jquery-3.2.1.js"
  integrity="sha256-DZAnKJ/6XZ9si04Hgrsxu/8s717jcIzLy3oi35EouyE="
  crossorigin="anonymous"
></script>

<script src="js/notes.js" defer></script>
```

The first line includes the jQuery library from a CDN (the integrity and crossorigin attributes are for [security](https://www.w3.org/TR/SRI/) when using outside resources). The second line includes the app’s JavaScript and only has one change from the original template — the `defer` attribute. This ensures that the `notes.js` file is loaded only after the DOM is ready (before this attribute existed, you had to resort to tactics such as putting the script tag at the end of the body tag or using a `DOMContentLoaded` event listener wrapping your entire code).

### Display note titles from an array of notes

Now that jQuery’s installed, let’s change the starting template to generate the dynamic notes list using JavaScript. First we'll representing the notes as an array of objects, where each object has an id, a body, and a timestamp. The `Date.now()` returns the number of milliseconds elapsed since 1 January 1970 00:00:00 UTC.

```javascript
var notes = [
  { id: 1, body: "This is a first test", timestamp: Date.now() },
  { id: 2, body: "This is a second test", timestamp: Date.now() },
  { id: 3, body: "This is a third test", timestamp: Date.now() },
];
```

Once we have the notes data, we can delete the placeholder HTML content and generate the note selectors ourselves:

```javascript
notes.forEach(function(note) {
  $('.note-selectors').append(
    '<div class="note-selector">' +
      '<p class="note-selector-title">' + note.body + '</p>' +
      '<p class="note-selector-timestamp">' + note.timestamp + '</p>' +
    '</div>'
  );
});
```

Here I’m using jQuery to both create new DOM elements and append them directly into the DOM. Right now I’m temporarily using the body and integer timestamp directly as the title and timestamp — they’ll need to be formatted appropriately to look pretty. 

You can check out a live demo [here](https://jsfiddle.net/peterxjang/w2xc3kkm).

> Note that there is an additional concern that’s more subtle — adding the `note.body` directly into the DOM here can open up an [XSS vulnerability](https://www.acunetix.com/websitesecurity/cross-site-scripting/), where a malicious user can enter a `<script>` tag in the HTML input and run unwanted JavaScript. You would need to sanitize the input yourself (oddly enough jQuery’s `.text()` method [won’t be enough here](https://watchitlater.com/blog/2011/10/cross-site-scripting-vulnerability-with-javascript-and-jquery/)), which is one disadvantage of using a library over a framework (which generally have such security features built in).

### Use methods to sort and format notes

Now let’s make helper methods to make sure the notes are sorted (newest first) and formatted properly (titles should be computed from the body, timestamps should be converted from milliseconds into a human readable string).

```javascript
function transformNotes(notes) {
  return notes.slice().sort(function (a, b) {
    return b.timestamp - a.timestamp;
  });
}

function formatTitle(body) {
  var maxLength = 20;
  if (body.length > maxLength) {
    return body.substring(0, maxLength - 3) + "...";
  } else if (body.length === 0) {
    return "New note";
  } else {
    return body;
  }
}

function formatTimestamp(timestamp) {
  return new Date(timestamp).toUTCString();
}
```

The logic here is somewhat simplified — the title should account for line breaks, the string output from the native `toUTCString` isn’t the prettiest, etc. However, since they are separate pure functions, it will be simple to make them more robust later. The loop would be modified to use the functions as follows:

```javascript
transformNotes(notes).forEach(function(note) {
  $('.note-selectors').append(
    '<div class="note-selector">' +
      '<p class="note-selector-title">' + formatTitle(note.body) + '</p>' +
      '<p class="note-selector-timestamp">' + formatTimestamp(note.timestamp) + '</p>' +
    '</div>'
  );
});
```

### Select a note on title click

Now let’s implement the ability to actually select notes. Clicking on a note title should both highlight the selected note on the left as well as display the contents in the editor on the right. First we need to keep track of a new variable `selectedNote` (which we’ll default to the first note).

```javascript
var notes = [
  {id: 1, body: "This is a first test", timestamp: Date.now()},
  {id: 2, body: "This is a second test", timestamp: Date.now()},
  {id: 3, body: "This is a third test", timestamp: Date.now()}
];
var selectedNote = notes[0];
```

Now comes the tricky part — in order to be able to click on a note title and display its corresponding information, we need to create a binding between the DOM and the data in JavaScript. One way to pull this off is by embedding the relevant data into the DOM using [HTML data-* attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes). In this case, each time I’m creating a new note selector DOM element with jQuery, I want to add the relevant note data into the DOM element itself using `data-id`, `data-body`, and `data-timestamp` attributes. For the sake of code organization, I extracted the code that creates the note selector DOM elements to a function called `domCreateNoteSelectors`:

```javascript
function domCreateNoteSelectors(notes, selectedNote) {
  transformNotes(notes).forEach(function(note) {
    var $noteSelector = $(
      '<div class="note-selector' + (note === selectedNote ? ' active' : '') + '">' +
        '<p class="note-selector-title">' + formatTitle(note.body) + '</p>' +
        '<p class="note-selector-timestamp">' + formatTimestamp(note.timestamp) + '</p>' +
      '</div>'
    );
    $noteSelector.data(note);
    $('.note-selectors').append($noteSelector);
  });
}
```

Note the variable name `$noteSelector` is using the convention of putting a $ in front of any variable which represents a DOM element. The key line which binds the data in JavaScript to the DOM is:

```javascript
$noteSelector.data(note);
```

This handy jQuery method takes all the keys in the `note` object (in this case `id`, `body`, and `timestamp`) and creates corresponding data-* attributes in the DOM element (in this case `data-id`, `data-body`, and `data-timestamp`).

Now that we have data binding, we’ll need to make an event listener to trigger whenever the user clicks on a note title:

```javascript
$(".note-selectors").on("click", ".note-selector", function () {
  $(".note-selector").removeClass("active");
  $(this).addClass("active");
  domUpdateNoteEditor($(this).data());
});
```

(Note that I’m putting a delegated event listener on the `.note-selectors` DOM element and not on the `.note-selector` directly— this is because the `.note-selector` elements don’t exist until later!) The event listener has two responsibilities — one is to update the style such that the selector appears highlighted, which is achieved by removing the `.active` class from all note selectors, then adding it back to the note selector that was just clicked. The other responsibility is to update the note editor on the right to display the appropriate note data. The note data is extracted from the DOM using `$(this).data()`, and is passed to a separate `domUpdateNoteEditor` function:

```javascript
function domUpdateNoteEditor(selectedNote) {
  $(".note-editor-info").html(formatTimestamp(selectedNote.timestamp));
  $(".note-editor-input").val(selectedNote.body);
}
```

You can check out a live demo [here](https://jsfiddle.net/peterxjang/nkpg036t).

### Edit the selected note on editor input

Next we’ll bind the `<textarea>` to the note data. Each time the user changes the text, there’s quite a few things that need to be updated:

- The underlying note data (body and timestamp)
- The displayed timestamp in the note editor
- The displayed title and timestamp in the current note selector
- The sort order of the note selectors (the current one should be on top)

Here’s the code to make all these changes happen:

```javascript
$(".note-editor-input").on("input propertychange", function (event) {
  // Update the note data
  var body = $(this).val();
  var timestamp = Date.now();
  $(".note-selector.active").data("body", body);
  $(".note-selector.active").data("timestamp", timestamp);

  // Update the DOM note editor timestamp
  $(".note-editor-info").html(formatTimestamp(timestamp));

  // Update the DOM currently selected note selector
  $(".note-selector.active .note-selector-title").html(formatTitle(body));
  $(".note-selector.active .note-selector-timestamp").html(formatTimestamp(timestamp));

  // Update the DOM note selectors sorting order
  var $active = $(".note-selector.active").detach();
  $(".note-selectors").prepend($active);
});
```

This is where we start to see some difficulties with jQuery — while the DOM manipulation code itself is fairly straightforward, it becomes more and more complicated to remember the different parts of the DOM that need to be updated with each change.

### Create a new note with a button

Now let’s implement the ability to create a new note. Clicking on the “New” button should create a new note (new id, no body, current timestamp). The new note should become the currently selected note and appear at the top of the list of note selectors. 

Originally we made the `domCreateNoteSelectors` function to create note selectors in a loop. We’ll need to extract the code to make a single note selector into a separate `domCreateNoteSelector` function (so we can reuse it with the new note button):

```javascript
function domCreateNoteSelector(note, selectedNote) {
  var $noteSelector = $(
    '<div class="note-selector' + (note === selectedNote ? ' active' : '') + '">' +
      '<p class="note-selector-title">' + formatTitle(note.body) + '</p>' +
      '<p class="note-selector-timestamp">' + formatTimestamp(note.timestamp) + '</p>' +
    '</div>'
  );
  $noteSelector.data(note);
  return $noteSelector;
}

// ...

function domCreateNoteSelectors(notes, selectedNote) {
  transformNotes(notes).forEach(function (note) {
    var $noteSelector = domCreateNoteSelector(note, selectedNote);
    $noteSelector.data(note);
    $(".note-selectors").append($noteSelector);
  });
}
```

Here’s the code that runs when the user clicks the “New” button (note that I added the `.toolbar-button-new` class in the HTML to make it easier to target the new button element):

```javascript
$(".toolbar-button-new").on("click", function () {
  $(".note-selector").removeClass("active");
  var note = {
    id: Date.now(),
    body: "",
    timestamp: Date.now(),
  };
  var $noteSelector = domCreateNoteSelector(note, note);
  $(".note-selectors").prepend($noteSelector);
  domUpdateNoteEditor(note);
});
```

Here I’m using `Date.now()` as a quick and dirty way of generating a id, it should be replaced with a more robust approach for a guaranteed unique id. In order to make sure the new note is highlighted, I first removed the `.active` class from all note selectors, then created the new note selector with the `domCreateNoteSelector` function, which takes in the note to create along with the currently selected note (which in this case is itself). This works fine, but again it’s getting a little harder to follow exactly how the DOM is being updated.

You can check out a live demo [here](https://jsfiddle.net/peterxjang/rnemvjct).

### Delete the selected note with a button

The delete button is in theory easy enough to implement — add an event listener to the “Delete” button which removes the currently selected note selector (which is marked with the class `.active`) from the DOM.

```javascript
$(".toolbar-button-delete").on("click", function () {
  $(".note-selector.active").remove();
});
```

Unfortunately, this doesn’t address what note should be marked as the currently selected note after the current one is deleted. Here’s a second attempt:

```javascript
$('.toolbar-button-delete').on('click', function() {
  $('.note-selector.active').remove();
  var children = $('.note-selectors').children();
  var $noteSelector = $(children[0]);
  $noteSelector.addClass('active');
  domUpdateNoteEditor($noteSelector.data());
});
```

This code will grab all the note selectors from the DOM and select the first one. Unfortunately, this doesn’t address what should happen when you delete the LAST note. Let’s say we want the app to hide the note editor completely if there are no notes:

```javascript
$('.toolbar-button-delete').on('click', function() {
  $('.note-selector.active').remove();
  var children = $('.note-selectors').children();
  if (children.length > 0) {
    var $noteSelector = $(children[0]);
    $noteSelector.addClass('active');
    domUpdateNoteEditor($noteSelector.data());
  } else {
    $('.note-editor').hide();
  }
});
```

This works, but once the note editor is hidden, it will never unhide! So now we have to make sure the note editor is shown when you create a new note:

```javascript
$(".toolbar-button-new").on("click", function () {
  $(".note-editor").show();
  //...
});
```

This completely works now, but we’re getting into the territory of what is known as “jQuery spaghetti” — the delete note feature requires writing code in both the delete button event listener as well as the new button event listener, which is highly unintuitive. As more features get added to the app, these types of unrelated dependencies start cropping up in more and more places, making it harder to reason about the code.

### Filter notes on search input

The final feature I’m going to implement is to be able to search notes immediately as you type in the search input. Before we implement it, I’m first going to extract out the code from the delete event listener that selects the appropriate note when the list changes into a function called `domSelectDefaultChild`, since we’ll need similar functionality when dynamically searching.

```javascript
function domSelectDefaultChild() {
  var children = $('.note-selector:visible');
  if (children.length > 0) {
    var $noteSelector = $(children[0]);
    if ($('.note-selector.active').length === 0) {
      $noteSelector.addClass('active');
      domUpdateNoteEditor($noteSelector.data());
    }
  } else {
    $('.note-editor').hide();
  }
}

// ...

$('.toolbar-button-delete').on('click', function() {
  $('.note-selector.active').remove();
  domSelectDefaultChild();
});
```

Now let’s add an event listener on the search input. This code will need to do the following things:

- Show the note editor in case it’s hidden
- Get the search text the user entered
- Loop through each note selector and show or hide it based on whether or not the note’s body (in the DOM data-body attribute) matches the user’s search text
- Select a new note if necessary

The code to accomplish this is as follows:

```javascript
$(".toolbar-search").on("input propertychange", function () {
  $(".note-editor").show();
  var searchNoteText = $(this).val();
  $(".note-selector").each(function () {
    var $note = $(this);
    if ($note.data().body.toLowerCase().indexOf(searchNoteText.toLowerCase()) === -1) {
      $note.hide();
      if ($note.hasClass("active")) {
        $note.removeClass("active");
      }
    } else {
      $note.show();
    }
  });
  domSelectDefaultChild();
});
```

You can see the final working example in all its glory [here](https://jsfiddle.net/peterxjang/a0qj4njz).

### Conclusion

I’ve noted in a couple of places where the jQuery code is starting to get hard to manage. Still, I would consider this app to be reasonably simple, so jQuery works well enough here, especially if you’re diligent about extracting reusable functions. There’s a certain beauty to jQuery where the code closely matches what you’re trying to accomplish in terms of DOM manipulation. However, it does get harder and harder to keep track of how the code is working as you add more features.

The approaches I’ve taken here certainly don’t represent the best or only way to implement these features. You may be able to better organize the code using advanced features like [mutation observers](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver), modules, etc. jQuery is unopinionated and allows you to come up with your own patterns to organize your code. But at that point, you may want to look at frameworks which have already established a set of patterns to make it easier to create new apps. In the [next part](comparing-frontend-approaches-part-3-vuejs.html) of this series, we’ll take a look at building the same set of features from scratch using the [Vue.js](https://vuejs.org/) framework. Stay tuned!
