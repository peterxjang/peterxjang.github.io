---
title: "Comparing Frontend Approaches Part 7: Final Thoughts"
description: "A look at jQuery, Vue.js, React, and Elm"
date: 2017-09-27
categories: []
keywords: []
---

- [Part 1: Introduction](comparing-frontend-approaches-part-1-introduction.html)
- [Part 2: jQuery](comparing-frontend-approaches-part-2-jquery.html)
- [Part 3: Vue.js](comparing-frontend-approaches-part-3-vuejs.html)
- [Part 4: Vue.js with components](comparing-frontend-approaches-part-4-vuejs-with-components.html)
- [Part 5: React](comparing-frontend-approaches-part-5-react.html)
- [Part 6: Elm](comparing-frontend-approaches-part-6-elm.html)
- **Part 7: Final thoughts**

![]({{ 'img/1__i1kwW5Jx__F3nYv0AsPlsXg.jpeg' | url }})

If you’ve made it this far, congratulations! Here’s a brief summary of the pros and cons of each approach for building this particular notes app:

#### jQuery

A direct DOM manipulation based approach.   
**Pros**: Easy to install, easy to learn. Smaller in size than most frameworks.  
**Cons**: Requires diligence with organizing code. Making all appropriate DOM updates when something changes can get complex (“jQuery spaghetti”).

#### Vue.js

A template based approach (with a virtual DOM under the hood)  
**Pros**: Easy to install, easy to learn. Smallest size in terms lines of code, one of the smallest in terms of payload.  
**Cons**: Non-trivial amount of Vue specific syntax to learn. Can get harder to organize code in the single file and Vue instance as the app increases in complexity.

#### Vue.js with components

The same Vue.js approach, this time with components  
**Pros**: Single file components reduces cognitive load. Increased reusability and simplicity with components.  
**Cons**: Requires complex build step. Passing data down and bubbling events up can be tedious with multiple parent-child components.

#### React

A virtual DOM based component approach
**Pros**: Small API surface. Immutable state improves simplicity. Increased reusability and simplicity with components.  
**Cons**: Requires complex build step. Harder to learn with heavy use of advanced and experimental JS features. Functional approach in JS requires particular care.

#### Elm

A statically typed, purely functional approach with virtual DOM  
**Pros**: Static types and immutable data prevent runtime errors. Clear code structure with The Elm Architecture. Smallest payload (as of version 0.19).  
**Cons**: Harder to learn (a totally separate language from JS). Side effects require extra work to wire up.

Note that the purpose here has never been to help you choose which frontend approach is “best”. The answer is always it depends on the context. For an app this small in scale, Vue.js (without components) seems like a good fit, especially since it has the smallest payload and the code is well structured. _(Edit: Elm version 0.19 has made a lot of_ [_optimizations to reduce payload size_](http://elm-lang.org/blog/small-assets-without-the-headache) _and currently is significantly smaller than all other approaches here!)_ But most apps tend to increase in scope, and even your most fundamental assumptions can change as you add, remove, or modify features.

Context also includes a person’s current level of knowledge. What’s easy for you might be hard for someone else, and vice versa. What’s hard for you today may be easy for you a year from now. This means Elm might be a terrible choice for someone with a tight deadline and no experience with functional programming, and yet would be a perfect choice for someone already experienced with Elm. All frontend approaches covered here have great merit, and the honest best choice has more to do with your team and company culture than it does with some definitive “winner”.

Going back to the original idea of learning a new programming language a year, my hope is that you gained a better understanding of how each frontend approach works. This will help you write better code, regardless of your choice of library or framework. I also hope this inspired you to dig deeper into a frontend approach, whether you’re new to functional programming or a React developer who never learned jQuery. Thanks for reading!
