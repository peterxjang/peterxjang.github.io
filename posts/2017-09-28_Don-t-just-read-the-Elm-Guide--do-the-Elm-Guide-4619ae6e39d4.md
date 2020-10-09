---
title: "Don’t just read the Elm Guide, do the Elm Guide"
description: My experiences getting started with learning Elm
date: "2017-09-28T23:28:37.671Z"
categories: []
keywords: []
---

Recently I’ve been teaching myself the [Elm programming language](http://elm-lang.org/), something I’ve been wanting to do ever since I saw the [Elm Debugger: Mario](http://debug.elm-lang.org/edit/Mario.elm) example in 2014. About 100 lines of code with sprite animation, keyboard input, and a time-traveling debugger — this really blew me away, I had never seen anything like it!

![Elm game with time-traveling debugger](img/1__yPn19xbe1wjCo2c4os__vDA.gif)

I put off really digging into Elm for various reasons — there were a lot of new concepts, the [formatting style](http://elm-lang.org/docs/style-guide) (particularly commas on new lines) was pretty jarring to me, etc. Recently, I’ve heard several people describe the best way to learn Elm is **not** focus on theory or syntax — instead just start to build stuff using [The Elm Architecture](https://guide.elm-lang.org/architecture/), a structured pattern to building Elm apps, and learn along the way.

This is the basic teaching approach of the official [Elm Guide](https://guide.elm-lang.org), which I highly, highly recommend — it’s not just that it’s very well explained (which it is), but it’s also very carefully thought out in terms of the order each concept is introduced. I’ve been following this approach and it’s helped me get to the point where I can [build Elm apps](https://medium.com/@peterxjang/comparing-frontend-frameworks-part-6-elm-578714526164) on my own! Here I’m going to provide some tips to go along with the first three sections of the Elm Guide that really helped me solidify my learning.

#### Section 1: [Install](https://guide.elm-lang.org/install.html)

The Elm Guide gives information on how to install Elm, recommends plugins for your text editor of choice, and optionally recommends [elm-format](https://github.com/avh4/elm-format), which is a tool to automatically format your code (like [gofmt](https://golang.org/cmd/gofmt/) for Go and [prettier](https://github.com/prettier/prettier) for JavaScript).

**To me, elm-format is not optional, it is an absolute must**. Elm’s syntax can be hard for a newcomer to get used to — with elm-format, you simply don’t need to spend time to get used to it, your code will automatically format itself! It may seem tedious to do editor and tooling setup first, as people often want to play around with the code immediately. However, if you’re taking the language seriously, you’ll have to set up your tools at some point, and in this case the tooling reduces the barriers to learning in a big way!

#### Section 2: [Core Language](https://guide.elm-lang.org/core_language.html)

The Elm Guide then walks through some of the basic language features. In many ways Elm is similar enough to JavaScript, to the point where you can play around with it immediately using `elm-repl` in your terminal. As the guide says, **you should type out each example — do not just read the text**. The concepts aren’t likely to stick otherwise.

One other point is to try not to jump ahead — if you’ve seen Elm code in the wild, you may be tempted to learn and try out more advanced syntax and concepts. The guide does cover these things in detail later, I would recommend you go in order so you don’t get overwhelmed at the start.

#### Step 3: [The Elm Architecture](https://guide.elm-lang.org/architecture/)

This is the big one — the Elm Guide describes the basic concept of The Elm Architecture, then walks you through several examples of small but fully working apps. The guide suggests that you clone the code for each example from an existing GitHub repo or follow along in the online editor in an existing project.

I personally think you shouldn’t clone the code or use the online editor from an existing project— instead, **I believe** **you should retype each example in your own text editor and run the code yourself**. Each example is a single file with a reasonably small amount of code. Here’s the first example in its fully working entirety:

```elm
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)


main =
  Html.beginnerProgram { model = model, view = view, update = update }


-- MODEL

type alias Model = Int

model : Model
model =
  0


-- UPDATE

type Msg = Increment | Decrement

update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (toString model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```

Instead of copying and pasting this code, you should retype it from scratch. It’s only about 50 lines of code, half of which is whitespace (which elm-format will handle for you). Retyping it all will help you get used to writing Elm in your text editor, it will help you get used to your command line tools (`elm-package`, `elm-reactor`, `elm-make`), and it will help you get used to Elm syntax and style (essentially through osmosis).

There’s a lot going on here that’s brand new (import statements, type alias vs type annotations, union types, etc.). The goal isn’t to understand it all at once, the goal is to get the “feel” of Elm and let the compiler help fix mistakes. Cloning or copying and pasting prevents you from really feeling the structure and naming conventions here.

#### Step 3.1: [The Elm Architecture](https://guide.elm-lang.org/architecture/) — refactoring examples

One of the more tricky but subtle points in learning a new language is understanding which parts of the code are variables, which parts are keywords, which parts are function inputs, etc. Let’s look at one of the first lines in the above example:

```elm
main =
  Html.beginnerProgram { model = model, view = view, update = update }
```

If you’ve been doing Elm for a while, this line of code is easy enough to understand. If you’re new to Elm, it can be pretty confusing! The name `model` appears all over the code — sometimes it’s a record field, sometimes it’s a local variable, sometimes it’s an input to a function. This can make it hard to tease things apart.

Here’s something I recommend to improve your knowledge of any working code that you don’t fully understand: **rename variables using overly explicit names as an exercise**. Here are the rules I’m going to follow:

- Rename any functions you define with “run” in front   
  (`sum` becomes `runSum`)
- Rename any function arguments with “input” in front   
  (`number` becomes `inputNumber`)
- Rename any non-function variables you define with “my” in front   
  (`name` becomes `myName`)
- Rename any types with “Type” in front   
  (`SelectAll` becomes `TypeSelectAll`)

Here’s what the earlier example looks like refactored with these overly explicit names:

```elm
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)

main =
    Html.beginnerProgram { model = myModel, view = runView, update = runUpdate }
    

-- MODEL

type alias TypeModel = Int

myModel : TypeModel
myModel =
    0
    

-- UPDATE

type TypeMsg
    = TypeIncrement
    | TypeDecrement
    
runUpdate : TypeMsg -> TypeModel -> TypeModel
runUpdate inputMsg inputModel =
    case inputMsg of
        TypeIncrement ->
            inputModel + 1  

        TypeDecrement ->
            inputModel - 1
            
            
-- VIEW

runView : TypeModel -> Html TypeMsg
runView inputModel =
    div []
        [ button [ onClick TypeDecrement ] [ text "-" ]
        , div [] [ text (toString inputModel) ]
        , button [ onClick TypeIncrement ] [ text "+" ]
        ]
```

Here it’s a lot more explicit when `model` is a record field, `myModel` is a local variable, `TypeModel` is a type, and `inputModel` is an argument to a function. If you think this looks **more** confusing than the original version, you may be right — however, if you can’t mentally translate between one version and the other, it likely indicates that you don’t grasp the code flow as well as you should.

The nice part of this exercise with Elm in particular is that Elm has a great compiler that makes this type of refactoring fun. **Note that I’m not suggesting to write production code using this naming convention** — this is only a learning exercise. It gives you more practice typing and creating Elm code in a safe environment, and I find that by making the names more explicit, the concepts in the code become more clear.

I pretty much gave away the answer to the refactoring exercise for the first example, but luckily there are many more in the Elm Guide for you to try. If you’re curious for another example, you can also check out [the Elm part](https://medium.com/@peterxjang/comparing-frontend-frameworks-part-6-elm-578714526164) of my series on [building a notes app using different frontend approaches](https://medium.com/@peterxjang/comparing-frontend-frameworks-part-1-introduction-6cf3d49e42cf). I hope you find some of these tips useful on your journey to learning Elm!
