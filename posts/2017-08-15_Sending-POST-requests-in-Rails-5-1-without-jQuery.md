---
title: Sending POST requests in Rails 5.1 without jQuery
description: >-
  Since the jQuery library was removed as a hard dependency in Rails 5.1, it’s a
  little harder to send AJAX requests from JavaScript. jQuery…
date: "2017-08-15T15:58:15.539Z"
---

Since the jQuery library was removed as a hard dependency in Rails 5.1, it’s a little harder to send AJAX requests from JavaScript. jQuery made it easy for a couple of reasons. One, it provided helper functions like `$.get`, `$.post`, and the more general`$.ajax`. Two, it automatically included the CSRF token in the header of the request. Without the CSRF token that Rails generates, Rails will by default reject any non-GET requests, even for same-origin requests.

Despite not having jQuery, Rails 5.1 actually does provide a way to send AJAX requests! Rails was able to drop the jQuery dependency simply by writing their own helper functions in vanilla JavaScript. These helpers are included in every new Rails app as `rails-ujs` (you can see the source [here](https://github.com/rails/rails/tree/master/actionview/app/assets/javascripts)). This gives your JavaScript access to an object called `Rails` (as opposed to `$`). One of the helpers is `Rails.ajax`, which is very similar to jQuery’s `$.ajax`. Here is an example of a GET request:

```javascript
Rails.ajax({
  url: "/api/v1/people.json",
  type: "GET",
  success: function (data) {
    console.log(data);
  },
});
```

Easy enough, especially if you’re familiar with jQuery. (Note that this isn’t a 100% compatible drop in replacement for `$.ajax`, it was simply written to resemble the API of `$.ajax`). Here is an example of a POST request:

```javascript
Rails.ajax({
  url: "/api/v1/people.json",
  type: "POST",
  data: "first_name=Ricky&last_name=Bobby",
  success: function (data) {
    console.log(data);
  },
});
```

Two things to note about this POST request. One, it’s automatically sending the CSRF token, which means the request will go through without any additional configuration (nice!). Two, the `data` must be formatted as string form parameters, you can’t give it a JavaScript object, at least at the time of this writing with Rails 5.1.3 (not so nice!). Note that it should be trivial to set the `"Content-Type"` of the request to `"application/json"`, which would allow you to send a stringified JavaScript object, but it doesn’t seem to be possible with the way `rails-ujs` is written as of now.

If not being able to send POST data as JSON is a deal breaker, you can instead use other approaches like the new `fetch` API. In that case, you would be responsible for finding the CSRF token in the DOM and sending it with the request headers. There’s actually a helper function in `rails-ujs` that can grab the CSRF token — `Rails.csrfToken()`. Here’s the same POST request using the `fetch` API instead:

```javascript
fetch("/api/v1/people.json", {
  method: "post",
  body: JSON.stringify({
    first_name: "Ricky",
    last_name: "Bobby",
  }),
  headers: {
    "Content-Type": "application/json",
    "X-CSRF-Token": Rails.csrfToken(),
  },
  credentials: "same-origin",
})
  .then(function (response) {
    return response.json();
  })
  .then(function (data) {
    console.log(data);
  });
```

The advantage to this approach is that you can send POST data using any format you want, whether it’s JSON or string form parameters. The disadvantage is that `fetch` isn’t 100% compatible with every browser yet, and it’s a little harder for beginners to understand. In either case, be sure to take advantage of the `rails-ujs` helper functions in your JavaScript to send AJAX requests with ease!
