---
title: "Comparing Frontend Approaches Part 6: Elm"
description: "A look at jQuery, Vue.js, React, and Elm"
date: "2017-09-26T03:49:30.727Z"
categories: []
keywords: []
---

- [Part 1: Introduction](comparing-frontend-approaches-part-1-introduction.html)
- [Part 2: jQuery](comparing-frontend-approaches-part-2-jquery.html)
- [Part 3: Vue.js](comparing-frontend-approaches-part-3-vuejs.html)
- [Part 4: Vue.js with components](comparing-frontend-approaches-part-4-vuejs-with-components.html)
- [Part 5: React](comparing-frontend-approaches-part-5-react.html)
- **Part 6: Elm**
- [Part 7: Final thoughts](comparing-frontend-approaches-part-7-final-thoughts.html)

![](img/1__NQppPMuv7xYB1IB4hF4AnQ.png)

In this part we will be implementing the [web based clone](https://medium.com/@peterxjang/comparing-frontend-frameworks-part-1-introduction-6cf3d49e42cf) of the [Mac Notes app](https://support.apple.com/kb/PH22608?locale=en_US) using [Elm](http://elm-lang.org/). Elm (which is not a JavaScript framework but a completely different language that compiles down to JavaScript) came out in 2012 and is currently gaining a lot of traction, with more and more companies [using it in production](https://github.com/lpil/elm-companies).

Elm is a statically typed purely functional language inspired by Haskell, but with a design that makes it easier for beginners to functional programming to get started. It uses a virtual DOM approach similar to React (even though the two were created independently). Elm doesn’t use a framework per se, but instead uses a pattern called [The Elm Architecture](https://guide.elm-lang.org/architecture/). Let’s dive in and check it out!

> Note — this article is not meant to teach Elm from scratch, but rather give an overview of what building an Elm app looks like in comparison to other JavaScript frontend approaches. You don’t need experience with the language to get something out of the article, but if you want a sense of the language before continuing, I would recommend reading this [Elm vs. JavaScript syntax comparison](http://elm-lang.org/docs/from-javascript) and looking over the [official guide](https://guide.elm-lang.org/) (which is an excellent resource for learning the language).

### Installation

Like [Vue.js with components](https://medium.com/@peterxjang/comparing-frontend-frameworks-part-4-vue-js-with-components-675c880d4585) and [React](https://medium.com/@peterxjang/comparing-frontend-frameworks-part-5-react-b51fd7d075fe), we’re going to need a build process to use Elm. First we need to install Elm itself if you don’t already have it on your computer:

```
$ npm install -g elm
```

Now we’re going to create a new directory and copy the HTML and CSS files from the original [notes app template](https://jsfiddle.net/peterxjang/vtsjc5w9/?utm_source=website&utm_medium=embed&utm_campaign=vtsjc5w9). The only thing left is the JavaScript, which we will generate from Elm code. First run the command:

```
$ elm init
```

This will prompt you to create an `elm.json` file (which is similar to JavaScript’s `package.json` file). By default it included the necessary dependencies for Elm to work with a web browser.

Next we’re going to create a new file called **`Notes.elm`**, which will contain our Elm code. Right now we aren’t going to add any new functionality, we just want to convert the HTML template into Elm code, like we did with [React](https://medium.com/@peterxjang/comparing-frontend-frameworks-part-1-introduction-6cf3d49e42cf) and JSX. However, Elm doesn’t use JSX — it uses plain old functions instead. Here’s what the **`Notes.elm`** file looks like:

```elm
module Notes exposing (..)

import Html exposing (..)
import Html.Attributes exposing (..)


main =
    div [ id "app" ]
        [ div [ class "toolbar" ]
            [ button [ class "toolbar-button" ] [ text "New" ]
            , button [ class "toolbar-button" ] [ text "Delete" ]
            , input [ class "toolbar-search", type_ "text", placeholder "Search..." ] []
            ]
        , div [ class "note-container" ]
            [ div [ class "note-selectors" ]
                [ div [ class "note-selector active" ]
                    [ p [ class "note-selector-title" ] [ text "First note..." ]
                    , p [ class "note-selector-timestamp" ] [ text "Timestamp here..." ]
                    ]
                , div [ class "note-selector" ]
                    [ p [ class "note-selector-title" ] [ text "Second note..." ]
                    , p [ class "note-selector-timestamp" ] [ text "Timestamp here..." ]
                    ]
                , div [ class "note-selector" ]
                    [ p [ class "note-selector-title" ] [ text "Third note..." ]
                    , p [ class "note-selector-timestamp" ] [ text "Timestamp here..." ]
                    ]
                ]
            , div [ class "note-editor" ]
                [ p [ class "note-editor-info" ] [ text "Timestamp here..." ]
                , textarea [ class "note-editor-input" ] [ text "First note..." ]
                ]
            ]
        ]
```

This is our first look at Elm code! Some notes:

- The first line is defining the code in this file as a module named `Notes`, which we will refer to later in the HTML.
- The next two lines are importing functions from the `Html` library.
- The next line `main =` is defining the main function. Functions in Elm do not have parentheses or keyword declarations, so they’re a bit hard to notice when coming from JavaScript.
- The rest of the code is defining the HTML using functions like `div`, `button`, `p`, etc. Each of these functions take two arguments — a list of attributes and a list of child nodes. So for example, a simple `<p>` tag would look like `p [class "info"] [text "Sample text"]`. This app starts with the parent `div [ id "app" ] [ … ]`, and every other HTML element is a child node of this parent and are inside the second argument list (which is a bit hard to see here).
- The [convention in Elm](http://elm-lang.org/docs/style-guide) is to write multi-line lists with commas at the start of each line instead of at the end, which can be pretty jarring coming from languages like JavaScript. The good news is that you can install a tool called [`elm-format`](https://github.com/avh4/elm-format), which can automatically format your Elm code (similar to [`gofmt`](https://golang.org/cmd/gofmt/) for Go and [`prettier`](https://github.com/prettier/prettier) for JavaScript). You can configure it to format your code every time you hit save in your editor, so you don’t need to worry about comma placement, indentation, or other style concerns!

To compile the code, we need to run this command in the terminal:

```
$ elm make Notes.elm --output js/notes.js
```

This will use the `elm make` command line tool to compile the Elm code in **`Notes.elm`** into a JavaScript file named `notes.js` in a folder named `js`. Note that it will also download the appropriate packages to a new folder called `elm-stuff` (similar to Node’s `node_modules` folder).

The last step is to edit the **`index.html`** file by removing all the HTML elements that now live in the Elm code and adding code to load up the Elm library:

```html/9-14/
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Notes</title>
  <link rel="stylesheet" href="css/notes.css"></link>
  <script src="js/notes.js"></script>
</head>
<body>
  <div id="app"></div>
    <script>
      var app = Elm.Notes.init({
        node: document.querySelector('#app')
      });
    </script>
</body>
</html>
```

Open the **`index.html`** file in your browser and you should see the familiar notes app starting point! You can see the code so far [here](https://github.com/peterxjang/notes-app-elm/tree/7913b8708a09c4482b307cd9bb47ae8f3f6ed8c3) for reference.

### Display note titles from an array of notes

Now we can start to make the HTML more dynamic by storing notes as a list (think JS array) of records (think JS objects) and looping through them to generate note selectors. We will do this by structuring the code using [The Elm Architecture](https://guide.elm-lang.org/architecture), which essentially boils down to defining 4 functions that Elm wires together to create the app. 

First let’s look at the main function itself. Here we’re using the `Browser.element` function that does all the work of wiring up our app. All we need to do is define each of the 4 functions — init, view, update, and subscriptions.

```elm
main =
    Browser.element
        { init = init
        , view = view
        , update = update
        , subscriptions = subscriptions
        }
```

Before we look at the 4 functions, let’s first look at how we’re defining the model, which is the data we’re using in our app (think `state` in React or `data` in Vue.js). Elm is a statically typed language, so we are defining the types on all the data up front. An individual Note type is defined with the line `type alias Note = { id : Int, body : String, timestamp : Int }`, which means a note must be a record with an integer id, string body, and integer timestamp. Now we define the `Model` itself with the line `type alias Model = { notes : List Note }`. This is saying that the `Model` must be a record with a key of `notes` and a value of a list of `Note` records. Defining these static types up front can seem like extra work, but they help prevent runtime errors later (if you forget to add a timestamp to a new note, etc.).

```elm
-- MODEL

type alias Note =
    { id : Int, body : String, timestamp : Int }


type alias Model =
    { notes : List Note }
```

Now let’s take a look at the `init` function, which initializes the model. The `init` function itself has a type annotation of `init : () -> ( Model, Cmd Msg )`, which means it must return a `Model` and a `Cmd` (command). Commands are how Elm handles side effects, which is a significant departure from the world of JavaScript. Elm is a pure functional language, which means functions aren’t allowed to have side effects, so you need different mechanisms to handle them. As of this moment, the init function doesn’t have any need for side effects so we’re outputting no command with `Cmd.none`. Also note that I’m leaving the timestamps as 0 for now — we need some sort of Elm equivalent to JavaScript’s `Date.now()`. But if you consider it closely, getting the current time is an impure function with side effects! Which means we actually will need a command in the `init` function, which we’ll wire up in a bit...

```elm
init : () -> ( Model, Cmd Msg )
init _ =
    ( { notes =
            [ { id = 1, body = "First note...", timestamp = 0 }
            , { id = 2, body = "Second note...", timestamp = 0 }
            , { id = 3, body = "Third note...", timestamp = 0 }
            ]
      }
    , Cmd.none
    )
```

The next function is the `update` function, which defines how the model gets updated. Right now the `update` function isn’t doing anything, so this is mostly placeholder code for now. The type annotation for the `update` function is `update : Msg -> Model -> ( Model, Cmd Msg )`, which says the function takes in two arguments (a `Msg` and a `Model`) and returns a tuple of type `( Model, Cmd Msg )`, which contains the updated model and a command for side effects. Right now our app doesn’t have any messages, so we have the line `type Msg = NoOp` as a placeholder for now — eventually we’ll have messages for selecting notes, clicking on the new button, etc.

```elm
-- UPDATE

type Msg
    = NoOp

update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    ( model, Cmd.none )
```

The next function is the `subscriptions` function, which are used to listen to events. Again, we’re not using subscriptions right now, so this code is essentially doing nothing. Subscriptions are for listening to events like mouse movements, web sockets, etc., which we won’t need for this app.

```elm
-- SUBSCRIPTIONS

subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.none
```

The final function is the `view` function, which defines the HTML output. This is the same code we started with to generate the HTML, broken down into sub-functions. The `view` function type annotation is `view : Model -> Html Msg`, which means it takes in the model (our list of notes) as the argument and outputs HTML.

```elm
-- VIEW

view : Model -> Html Msg
view model =
    div [ id "app" ]
        [ div [ class "toolbar" ]
            [ button [ class "toolbar-button" ] [ text "New" ]
            , button [ class "toolbar-button" ] [ text "Delete" ]
            , input [ class "toolbar-search", type_ "text", placeholder "Search..." ] []
            ]
        , div [ class "note-container" ]
            [ viewNoteSelectors model
            , div [ class "note-editor" ]
                [ p [ class "note-editor-info" ] [ text "Timestamp here..." ]
                , textarea [ class "note-editor-input" ] [ text "First note..." ]
                ]
            ]
        ]

viewNoteSelectors : Model -> Html Msg
viewNoteSelectors model =
    div [ class "note-selectors" ]
        (List.map (\note -> viewNoteSelector note) model.notes)

viewNoteSelector : Note -> Html Msg
viewNoteSelector note =
    div [ class "note-selector" ]
        [ p [ class "note-selector-title" ] [ text note.body ]
        , p [ class "note-selector-timestamp" ] [ text (String.fromInt note.timestamp) ]
        ]
```

You can see the complete **`Notes.elm`** file [here](https://github.com/peterxjang/notes-app-elm/blob/def4666dd0c9bc897ce9acd2c44ae23ef69020ee/Notes.elm).

Unlike our [React approach](https://medium.com/@peterxjang/comparing-frontend-frameworks-part-5-react-b51fd7d075fe), we aren’t going to break this app into components up front, or ever for that matter. We will be breaking things up into functions, but only when there’s a need to do so. In this case I made a sub-function `viewNoteSelectors` which is responsible for generating the `<div class="note-selectors">` HTML. It does this by mapping over the list of notes and calling up the `viewNoteSelector` function, which takes in a note and outputs the `<div class="note-selector">` HTML.

Although this seems like a lot of code to get started, keep in mind that every Elm app will consist of the same 4 functions (init, view, update, and subscriptions). This takes away a lot of the decision fatigue that comes with building frontend apps.

### Compute current timestamps

Now let’s circle back and properly compute the current timestamps. This was trivial in JavaScript, but since it involves side effects it takes more effort to wire up in Elm. First we need to install a new package to handle time:

```
$ elm install elm/time
```

Once it’s installed, you would change the code in **`Notes.elm`** as follows (I’m not showing the `subscriptions` and `view` functions since they didn’t change):

```text/5-6,32,38,42-48/
module Notes exposing (..)

import Browser
import Html exposing (..)
import Html.Attributes exposing (..)
import Task
import Time

main =
    Browser.element
        { init = init
        , view = view
        , update = update
        , subscriptions = subscriptions
        }
        
-- MODEL

type alias Note =
    { id : Int, body : String, timestamp : Int }
    
type alias Model =
    { notes : List Note }
    
init : () -> ( Model, Cmd Msg )
init _ =
    ( { notes =
            [ { id = 1, body = "First note...", timestamp = 0 }
            , { id = 2, body = "Second note...", timestamp = 0 }
            , { id = 3, body = "Third note...", timestamp = 0 }
            ]
      }
    , Task.perform InitializeNotesTimestamps Time.now
    )
    
-- UPDATE

type Msg
    = InitializeNotesTimestamps Time.Posix
    
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        InitializeNotesTimestamps time ->
            ( { model
                | notes = List.map (\note -> { note | timestamp = Time.posixToMillis time }) model.notes
              }
            , Cmd.none
            )
            
-- SUBSCRIPTIONS
-- ...

-- VIEW
-- ...
```

Let’s break this down piece by piece:

- First we import the `Task` and `Time` libraries, which are needed to create timestamps.
- The `init` function now runs a command with the [`Task.perform`](http://package.elm-lang.org/packages/elm-lang/core/latest/Task#perform) function, which takes in two arguments — in this case the `InitializeTimestamps` message and [`Time.now`](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Time#now), which gets the POSIX representation of time when the task is run (similar to JavaScript’s `Date.now()`, although it isn’t stored in Elm as an integer).
- The `update` section now has something to do! First we change the Msg definition to be a message called `InitializeNotesTimestamps` (which our init function will send out), which comes with the `time` result from `Time.now`.
- Now we can change the `update` function itself to respond to the case where the message is `InitializeNotesTimestamps` (right now this is the only possibility, but we will be adding more cases soon). In that case the function will update every note with the given `time` (which in this case must first be converted into milliseconds using `Time.posixToMillis`).

There’s no doubt that this is quite a bit of work in Elm for something that was trivial in JavaScript. Consider this an up front cost — this architecture provides some absolute guarantees about the safety and structure of the code. The purity in a functional language like Elm makes certain features with side effects harder to implement, but makes it literally impossible to be lazy and write something like jQuery spaghetti.

### Use functions to sort and format notes

Now let’s write more functions, this time to sort and format the notes. Here’s the code in **`Notes.elm`** (focusing on the changes in the `view` function):

```elm
-- VIEW

view : Model -> Html Msg  
view model =  
    div [ id "app" ]  
        [ div [ class "toolbar" ]  
            [ button [ class "toolbar-button" ] [ text "New" ]  
            , button [ class "toolbar-button" ] [ text "Delete" ]  
            , input [ class "toolbar-search", type_ "text", placeholder "Search..." ] []  
            ]  
        , div [ class "note-container" ]  
            [ viewNoteSelectors model  
            , div [ class "note-editor" ]  
                [ p [ class "note-editor-info" ] [ text "Timestamp here..." ]  
                , textarea [ class "note-editor-input" ] [ text "First note..." ]  
                ]  
            ]  
        ]

viewNoteSelectors : Model -> Html Msg  
viewNoteSelectors model =  
    div [ class "note-selectors" ]  
    (model.notes  
            |> List.sortBy .timestamp  
            |> List.reverse  
            |> List.map (note -> viewNoteSelector note)  
        )

viewNoteSelector : Note -> Html Msg  
viewNoteSelector note =  
    div [ class "note-selector" ]  
        [ p [ class "note-selector-title" ] [ text (formatTitle note.body) ]  
        , p [ class "note-selector-timestamp" ] [ text (formatTimestamp note.timestamp) ]  
        ]

-- HELPERS

formatTitle : String -> String  
formatTitle body =  
    let  
        maxLength =  
            20  
        length =  
            String.length body  
    in  
    if length > maxLength then  
        String.left (maxLength - 3) body ++ "..."  
    else if length == 0 then  
        "New note"  
    else  
        body

formatTimestamp : Int -> String  
formatTimestamp timestamp =  
    let  
        time =  
            Time.millisToPosix timestamp  
        hour =  
            String.fromInt (Time.toHour Time.utc time)  
        minute =  
            String.fromInt (Time.toMinute Time.utc time)  
        second =  
            String.fromInt (Time.toSecond Time.utc time)  
    in  
    hour ++ ":" ++ minute ++ ":" ++ second ++ " UTC"
```

Let’s go over the changes one at a time:

- In the `viewNoteSelectors` function, we are adding extra function calls to sort the notes by timestamp in reverse order. Note the use of the `|>` operator, which is an alternative syntax for passing arguments to functions (`(arg |> func)` is the same as `(func arg)`), which helps reduce parentheses.
- In the `viewNoteSelector` function, we are using the `formatTitle` and `formatTimestamp` functions, defined below.
- The `formatTitle` function is using the same logic we’ve used in the past. Note the use of the `let...in...` syntax, which is required when creating temporary variables in a function.
- The `formatTimestamp` function uses the Date library to format the given timestamp (like JavaScript’s `.toUTCString()`). I’m also using `String.slice` to remove parts of the formatted string that aren’t needed.

Note that the formatted timestamp isn’t as detailed as JavaScript’s `.toUTCString()`. Elm doesn’t come with an equivalent function, so I’m using a simplified formatting helper function that only returns the hours/minutes/seconds. To make it more detailed can get quite involved (getting the [user’s time zone](https://package.elm-lang.org/packages/elm/time/latest/Time#here), formatting [weekday](https://package.elm-lang.org/packages/elm/time/latest/Time#Weekday), [month names](https://package.elm-lang.org/packages/elm/time/latest/Time#Month), etc.), which is outside the scope of this article, so the simplified function will do for now.

### Select a note on title click

Now let’s implement the ability to actually select notes. Clicking on a note title should both highlight the selected note on the left as well as display the contents in the editor on the right. The code in **`Notes.elm`** will change accordingly:

```text/5,28,38,50,63-64,90,101,105-107,113-123/
module Notes exposing (..)
  
import Browser  
import Html exposing (..)  
import Html.Attributes exposing (..)  
import Html.Events exposing (..)  
import Task  
import Time  
  
  
main =  
    Browser.element  
        { init = init  
        , view = view  
        , update = update  
        , subscriptions = subscriptions  
        }  
  
  
  
-- MODEL  
  
  
type alias Note =  
    { id : Int, body : String, timestamp : Int }  
  
  
type alias Model =  
    { notes : List Note, selectedNoteId : Int }  
  
  
init : () -> ( Model, Cmd Msg )  
init _ =  
    ( { notes =  
            [ { id = 1, body = "First note...", timestamp = 0 }  
            , { id = 2, body = "Second note...", timestamp = 0 }  
            , { id = 3, body = "Third note...", timestamp = 0 }  
            ]  
      , selectedNoteId = 1  
      }  
    , Task.perform InitializeNotesTimestamps Time.now  
    )  
  
  
  
-- UPDATE  
  
  
type Msg  
    = InitializeNotesTimestamps Time.Posix  
    | SelectNote Int  
  
  
update : Msg -> Model -> ( Model, Cmd Msg )  
update msg model =  
    case msg of  
        InitializeNotesTimestamps time ->  
            ( { model  
                | notes = List.map (\note -> { note | timestamp = Time.posixToMillis time }) model.notes  
              }  
            , Cmd.none  
            )

        SelectNote id ->  
            ( { model | selectedNoteId = id }, Cmd.none )  
  
  
  
-- SUBSCRIPTIONS  
  
  
subscriptions : Model -> Sub Msg  
subscriptions model =  
    Sub.none  
  
  
  
-- VIEW  
  
  
view : Model -> Html Msg  
view model =  
    div [ id "app" ]  
        [ div [ class "toolbar" ]  
            [ button [ class "toolbar-button" ] [ text "New" ]  
            , button [ class "toolbar-button" ] [ text "Delete" ]  
            , input [ class "toolbar-search", type_ "text", placeholder "Search..." ] []  
            ]  
        , div [ class "note-container" ]  
            [ viewNoteSelectors model  
            , viewNoteEditor model  
            ]  
        ]  
  
  
viewNoteSelectors : Model -> Html Msg  
viewNoteSelectors model =  
    div [ class "note-selectors" ]  
        (model.notes  
            |> List.sortBy .timestamp  
            |> List.reverse  
            |> List.map (\note -> viewNoteSelector note model.selectedNoteId)  
        )  
  
  
viewNoteSelector : Note -> Int -> Html Msg  
viewNoteSelector note selectedNoteId =  
 div [ classList [ ( "note-selector", True ), ( "active", note.id == selectedNoteId ) ], onClick (SelectNote note.id) ]  
        [ p [ class "note-selector-title" ] [ text (formatTitle note.body) ]  
        , p [ class "note-selector-timestamp" ] [ text (formatTimestamp note.timestamp) ]  
        ]  
  
  
viewNoteEditor : Model -> Html Msg  
viewNoteEditor model =  
    case model.notes |> List.filter (\note -> note.id == model.selectedNoteId) |> List.head of  
        Nothing ->  
            div [ class "note-editor" ] []  
  
        Just selectedNote ->  
            div [ class "note-editor" ]  
                [ p [ class "note-editor-info" ] [ text (formatTimestamp selectedNote.timestamp) ]  
                , textarea [ class "note-editor-input" ] [ text selectedNote.body ]  
                ]

-- HELPERS

formatTitle : String -> String  
formatTitle body =  
    let  
        maxLength =  
            20

    length =  
            String.length body  
    in  
    if length > maxLength then  
        String.left (maxLength - 3) body ++ "..."

    else if length == 0 then  
        "New note"

    else  
        body

formatTimestamp : Int -> String  
formatTimestamp timestamp =  
    let  
        time =  
            Time.millisToPosix timestamp

        hour =  
            String.fromInt (Time.toHour Time.utc time)

        minute =  
            String.fromInt (Time.toMinute Time.utc time)

        second =  
            String.fromInt (Time.toSecond Time.utc time)  
    in  
    hour ++ ":" ++ minute ++ ":" ++ second ++ " UTC"
```

Let’s go over the changes one at a time:

- First we import the Html.Events functions, which we will use later.
- In the MODEL section, we are adding a new piece of data to keep track of in the model — the `selectedNoteId`, which we initialize to 1 to start off.
- In the UPDATE section, we add a [union type](https://guide.elm-lang.org/types/union_types.html) to Msg — now it can be a `InitializeTimestamps` or a `SelectNote` message. So we also add another case to the `update` function — if the message is `SelectNote`, we will update the `selectedNoteId` in the model accordingly.
- In the VIEW section, the `viewNoteSelector` function has the code `onClick (SelectNote note.id)`, which is binding the DOM click event to trigger the `SelectNote` message with the note’s id.
- In the VIEW section, the `viewNoteSelector` function also changed to take in one more argument, the `selectedNoteId`. This enables us to apply the right CSS `active` class to the div if the note is the selected note with the code `classList [ ( "note-selector", True ), ( "active", note.id == selectedNoteId ) ]`.
- In the VIEW section, we also extracted the code which generates the `<div class="note-editor">` into a separate function called `viewNoteEditor`. This function uses the model `notes` and `selectedNoteId` data to identify which note matches the `selectedNoteId`. If nothing matches, the note editor will be an empty div. If there is a match, then the note editor will show the editor with the appropriate formatted timestamp and body.

Here we see another benefit of Elm — it forces you to deal with the case where the `selectedNoteId` doesn’t match any of the notes. There’s literally no other way to write the code and have it compile. The zero notes scenario wasn’t going to be a problem until we implemented the delete functionality, but it’s reassuring to know that these safety guarantees are built in from the start!

### Edit the selected note on editor input

At this point we can select a note and it highlights accordingly, but the content of the selected note aren’t showing up in the note editor. We also need to be able to update the selected note as the user enters text in the editor. Here’s how the UPDATE and VIEW sections of the code would change in **`Notes.elm`**:

```text/6-7,23-42,44-61,112,119,127-129/
-- UPDATE  
  
  
type Msg  
    = InitializeNotesTimestamps Time.Posix  
    | SelectNote Int  
    | UpdateSelectedNoteBody String  
    | UpdateSelectedNoteTimestamp Time.Posix  
  
  
update : Msg -> Model -> ( Model, Cmd Msg )  
update msg model =  
    case msg of  
        InitializeNotesTimestamps time ->  
            ( { model  
                | notes = List.map (\note -> { note | timestamp = Time.posixToMillis time }) model.notes  
              }  
            , Cmd.none  
            )  
  
        SelectNote id ->  
            ( { model | selectedNoteId = id }, Cmd.none )  
  
        UpdateSelectedNoteBody newText ->  
            case getSelectedNote model of  
                Nothing ->  
                    ( model, Cmd.none )  
  
                Just selectedNote ->  
                    let  
                        updateSelectedNote note =  
                            if note.id == model.selectedNoteId then  
                                { note | body = newText }  
  
                            else  
                                note  
  
                        newNotes =  
                            List.map updateSelectedNote model.notes  
                    in  
                    ( { model | notes = newNotes }  
                    , Task.perform UpdateSelectedNoteTimestamp Time.now  
                    )  
  
        UpdateSelectedNoteTimestamp newTime ->  
            case getSelectedNote model of  
                Nothing ->  
                    ( model, Cmd.none )  
  
                Just selectedNote ->  
                    let  
                        updateSelectedNote note =  
                            if note.id == model.selectedNoteId then  
                                { note | timestamp = Time.posixToMillis newTime }  
  
                            else  
                                note  
  
                        newNotes =  
                            List.map updateSelectedNote model.notes  
                    in  
                    ( { model | notes = newNotes }, Cmd.none )  
  
  
  
-- SUBSCRIPTIONS  
  
  
subscriptions : Model -> Sub Msg  
subscriptions model =  
    Sub.none  
  
  
  
-- VIEW  
  
  
view : Model -> Html Msg  
view model =  
    div [ id "app" ]  
        [ div [ class "toolbar" ]  
            [ button [ class "toolbar-button" ] [ text "New" ]  
            , button [ class "toolbar-button" ] [ text "Delete" ]  
            , input [ class "toolbar-search", type_ "text", placeholder "Search..." ] []  
            ]  
        , div [ class "note-container" ]  
            [ viewNoteSelectors model  
            , viewNoteEditor model  
            ]  
        ]  
  
  
viewNoteSelectors : Model -> Html Msg  
viewNoteSelectors model =  
    div [ class "note-selectors" ]  
        (model.notes  
            |> List.sortBy .timestamp  
            |> List.reverse  
            |> List.map (\note -> viewNoteSelector note model.selectedNoteId)  
        )  
  
  
viewNoteSelector : Note -> Int -> Html Msg  
viewNoteSelector note selectedNoteId =  
    div [ classList [ ( "note-selector", True ), ( "active", note.id == selectedNoteId ) ], onClick (SelectNote note.id) ]  
        [ p [ class "note-selector-title" ] [ text (formatTitle note.body) ]  
        , p [ class "note-selector-timestamp" ] [ text (formatTimestamp note.timestamp) ]  
        ]  
  
  
viewNoteEditor : Model -> Html Msg  
viewNoteEditor model =  
    case getSelectedNote model of  
        Nothing ->  
            div [ class "note-editor" ] []  
  
        Just selectedNote ->  
            div [ class "note-editor" ]  
                [ p [ class "note-editor-info" ] [ text (formatTimestamp selectedNote.timestamp) ]  
                , textarea [ class "note-editor-input", onInput UpdateSelectedNoteBody, value selectedNote.body ] []  
                ]  
  
  
  
-- HELPERS  
  
  
getSelectedNote : Model -> Maybe Note  
getSelectedNote model =  
    model.notes |> List.filter (\note -> note.id == model.selectedNoteId) |> List.head
```

Let’s examine the changes:

- In the UPDATE section, the `Msg` now gets two more types — `UpdateSelectedNoteBody` and `UpdateSelectedNoteTimestamp`. The two are related — if the user enters text in the note editor, it will trigger the `UpdateSelectedNoteBody` case in the `update` function, which will update the body of the selected note. When it’s done, it will trigger the `Time.now` task, which will trigger the `UpdateSelectedNoteTimestamp` message when it’s ready. When the `update` function is called with the `UpdateSelectedNoteTimestamp` case, it will update the selected note with the given time. Note that it has to determine which note matches the `selectedNoteId`, which is logic we’ve already defined, so I’ve extracted the logic into a function called `getSelectedNote` which is defined later.
- In the VIEW section, we’ve refactored the `viewNoteEditor` to use the `getSelectedNote` function. We are also adding new attributes to the `textarea` element with the line `textarea [ class "note-editor-input", onInput InputNoteBody, value selectedNote.body ] []`. This is binding the DOM input event to trigger the `UpdateSelectedNoteBody` message, as well as binding the selected note’s body to the `textarea` value.
- In the HELPERS section, we define the `getSelectedNote` method, which is just the extracted logic of finding the selected note as before.

### Create a new note with a button

Now let’s implement the ability to create a new note. Clicking on the “New” button should create a new note (new id, no body, current timestamp). The new note should become the currently selected note and appear at the top of the list of note selectors. Here’s how the UPDATE and VIEW sections of the code would change in **`Notes.elm`**:

```text/8-9,45-46,48-58,69/
-- UPDATE  
  
  
type Msg  
    = InitializeNotesTimestamps Time.Posix  
    | SelectNote Int  
    | UpdateSelectedNoteBody String  
    | UpdateSelectedNoteTimestamp Time.Posix  
    | ClickNew  
    | CreateNote Time.Posix  
  
  
update : Msg -> Model -> ( Model, Cmd Msg )  
update msg model =  
    case msg of  
        InitializeTimestamps time ->  
            ( { model  
                | notes = List.map (\note -> { note | timestamp = time }) model.notes  
              }  
            , Cmd.none  
            )  
  
        SelectNote id ->  
            ( { model | selectedNoteId = id }, Cmd.none )  
  
        InputNoteBody newText ->  
            ( model, Time.now |> Task.perform (UpdateNote newText) )  
  
        UpdateNote newText newTimestamp ->  
            case getSelectedNote model of  
                Nothing ->  
                    ( model, Cmd.none )  
  
                Just selectedNote ->  
                    let  
                        updateSelectedNote note =  
                            if note.id == model.selectedNoteId then  
                                { note | body = newText, timestamp = newTimestamp }  
                            else  
                                note  
                    in  
                    ( { model | notes = List.map updateSelectedNote model.notes }  
                    , Cmd.none  
                    )  
  
        ClickNew ->  
            ( model, Time.now |> Task.perform CreateNote )  
  
        CreateNote newTimestamp ->  
            let  
                newId =  
                    round (Time.inMilliseconds newTimestamp)  
            in  
            ( { model  
                | notes = [ { id = newId, body = "", timestamp = newTimestamp } ] ++ model.notes  
                , selectedNoteId = newId  
              }  
            , Cmd.none  
            )  
  
  
  
-- VIEW  
  
  
view : Model -> Html Msg  
view model =  
    div [ id "app" ]  
        [ div [ class "toolbar" ]  
            [ button [ class "toolbar-button", onClick ClickNew ] [ text "New" ]  
            , button [ class "toolbar-button" ] [ text "Delete" ]  
            , input [ class "toolbar-search", type_ "text", placeholder "Search..." ] []  
            ]  
        , div [ class "note-container" ]  
            [ viewNoteSelectors model  
            , viewNoteEditor model  
            ]  
        ]
```

Let’s examine the changes:

- In the UPDATE section, the `Msg` now gets two more types — `ClickNew` and `CreateNote`. The two are related — if the user clicks the “New” button, it will trigger the `ClickNew` message, which will in turn trigger the `Time.now` task which will run the `CreateNote` message when it’s ready. At that point the `CreateNote` case in the `update` function will have the timestamp and can update the model with a new note and `selectedNoteId`.
- In the VIEW section, the only thing that changes is the “New” button — we add an `onClick ClickNew` attribute to the button, which binds the DOM on click event to trigger the `ClickNew` message.

Here we’re really starting to see the beauty of The Elm Architecture. Features like this are very simple to wire up, without any of the overhead that we saw with different JavaScript component approaches where we had to pass around a lot of info between parents and children.

### Delete the selected note with a button

Wiring up this feature is pretty similar to the new note feature, with a couple of extra considerations. Here’s how the UPDATE and VIEW sections of the code would change in **`Notes.elm`**:

```text/10,84-97,109,123,153-155/
-- UPDATE  
  
  
type Msg  
    = InitializeNotesTimestamps Time.Posix  
    | SelectNote Int  
    | UpdateSelectedNoteBody String  
    | UpdateSelectedNoteTimestamp Time.Posix  
    | ClickNew  
    | CreateNote Time.Posix 
    | ClickDelete  
  
  
update : Msg -> Model -> ( Model, Cmd Msg )  
update msg model =  
    case msg of  
        InitializeNotesTimestamps time ->  
            ( { model  
                | notes = List.map (\note -> { note | timestamp = Time.posixToMillis time }) model.notes  
              }  
            , Cmd.none  
            )  
  
        SelectNote id ->  
            ( { model | selectedNoteId = id }, Cmd.none )  
  
        UpdateSelectedNoteBody newText ->  
            case getSelectedNote model of  
                Nothing ->  
                    ( model, Cmd.none )  
  
                Just selectedNote ->  
                    let  
                        updateSelectedNote note =  
                            if note.id == model.selectedNoteId then  
                                { note | body = newText }  
  
                            else  
                                note  
  
                        newNotes =  
                            List.map updateSelectedNote model.notes  
                    in  
                    ( { model | notes = newNotes }  
                    , Task.perform UpdateSelectedNoteTimestamp Time.now  
                    )  
  
        UpdateSelectedNoteTimestamp newTime ->  
            case getSelectedNote model of  
                Nothing ->  
                    ( model, Cmd.none )  
  
                Just selectedNote ->  
                    let  
                        updateSelectedNote note =  
                            if note.id == model.selectedNoteId then  
                                { note | timestamp = Time.posixToMillis newTime }  
  
                            else  
                                note  
  
                        newNotes =  
                            List.map updateSelectedNote model.notes  
                    in  
                    ( { model | notes = newNotes }, Cmd.none )  
  
        ClickNew ->  
            ( model, Task.perform CreateNote Time.now )  
  
        CreateNote newTime ->  
            let  
                newTimestamp =  
                    Time.posixToMillis newTime  
  
                newId =  
                    newTimestamp  
            in  
            ( { model  
                | notes = [ { id = newId, body = "", timestamp = newTimestamp } ] ++ model.notes  
                , selectedNoteId = newId  
              }  
            , Cmd.none  
            )

        ClickDelete ->  
            let  
                newNotes =  
                    List.filter (\note -> note.id /= model.selectedNoteId) model.notes  
  
                firstVisibleNote =  
                    newNotes |> sortNotes |> List.head  
            in  
            case firstVisibleNote of  
                Nothing ->  
                    ( { model | notes = newNotes }, Cmd.none )  
  
                Just availableNote ->  
                    ( { model | notes = newNotes, selectedNoteId = availableNote.id }, Cmd.none )  
  

  
-- VIEW  
  
  
view : Model -> Html Msg  
view model =  
    div [ id "app" ]  
        [ div [ class "toolbar" ]  
            [ button [ class "toolbar-button", onClick ClickNew ] [ text "New" ]  
            , button [ class "toolbar-button", onClick ClickDelete ] [ text "Delete" ]  
            , input [ class "toolbar-search", type_ "text", placeholder "Search..." ] []  
            ]  
        , div [ class "note-container" ]  
            [ viewNoteSelectors model  
            , viewNoteEditor model  
            ]  
        ]  
  
  
viewNoteSelectors : Model -> Html Msg  
viewNoteSelectors model =  
    div [ class "note-selectors" ]  
        (model.notes  
            |> sortNotes  
            |> List.map (\note -> viewNoteSelector note model.selectedNoteId)  
        )  
  
  
viewNoteSelector : Note -> Int -> Html Msg  
viewNoteSelector note selectedNoteId =  
    div [ classList [ ( "note-selector", True ), ( "active", note.id == selectedNoteId ) ], onClick (SelectNote note.id) ]  
        [ p [ class "note-selector-title" ] [ text (formatTitle note.body) ]  
        , p [ class "note-selector-timestamp" ] [ text (formatTimestamp note.timestamp) ]  
        ]  
  
  
viewNoteEditor : Model -> Html Msg  
viewNoteEditor model =  
    case getSelectedNote model of  
        Nothing ->  
            div [ class "note-editor" ] []  
  
        Just selectedNote ->  
            div [ class "note-editor" ]  
                [ p [ class "note-editor-info" ] [ text (formatTimestamp selectedNote.timestamp) ]  
                , textarea [ class "note-editor-input", onInput UpdateSelectedNoteBody, value selectedNote.body ] []  
                ]  
  
  
  
-- HELPERS  
  
  
sortNotes : List Note -> List Note  
sortNotes notes =  
    notes |> List.sortBy .timestamp |> List.reverse
```

Let’s examine the changes:

- In the UPDATE section, the `Msg` now one more type— `ClickDelete`, which will be wired up to run whenever the user clicks the “Delete” button. The `update` function with the `ClickDelete` case has to both delete the selected note (using `List.filter`) and select a new note, which should be the top of the sorted notes. Here I extracted the logic to sort the notes into a separate `sortNotes` function (defined later) to keep the code DRY.
- In the VIEW section, the only significant change is the “Delete” button — we add an `onClick ClickDelete` attribute to the button, which binds the DOM on click event to trigger the `ClickDelete` message. The only other change is refactoring the `viewNoteSelectors` function to use the `sortNotes` function to keep the code DRY.

Again, this change was a lot simpler to make compared to our other approaches, particularly in this case because Elm forced us to handle the case of no notes earlier!

### Filter notes on search input

The final feature is to be able to search notes immediately as you type in the search input. Since this is the last feature, let’s look at the code in **`Notes.elm`** in its entirety:

```text/28,39,57,137,146-156,178,191,221-225,228-233,239/
module Notes exposing (..)  
  
import Browser  
import Html exposing (..)  
import Html.Attributes exposing (..)  
import Html.Events exposing (..)  
import Task  
import Time  
  
  
main =  
    Browser.element  
        { init = init  
        , view = view  
        , update = update  
        , subscriptions = subscriptions  
        }  
  
  
  
-- MODEL  
  
  
type alias Note =  
    { id : Int, body : String, timestamp : Int }  
  
  
type alias Model =  
    { notes : List Note, selectedNoteId : Int, searchNoteText : String }  
  
  
init : () -> ( Model, Cmd Msg )  
init _ =  
    ( { notes =  
            [ { id = 1, body = "First note...", timestamp = 0 }  
            , { id = 2, body = "Second note...", timestamp = 0 }  
            , { id = 3, body = "Third note...", timestamp = 0 }  
            ]  
      , selectedNoteId = 1  
      , searchNoteText = ""  
      }  
    , Task.perform InitializeNotesTimestamps Time.now  
    )  
  
  
  
-- UPDATE  
  
  
type Msg  
    = InitializeNotesTimestamps Time.Posix  
    | SelectNote Int  
    | UpdateSelectedNoteBody String  
    | UpdateSelectedNoteTimestamp Time.Posix  
    | ClickNew  
    | CreateNote Time.Posix  
    | ClickDelete  
    | InputSearch String  
  
  
update : Msg -> Model -> ( Model, Cmd Msg )  
update msg model =  
    case msg of  
        InitializeNotesTimestamps time ->  
            ( { model  
                | notes = List.map (\note -> { note | timestamp = Time.posixToMillis time }) model.notes  
              }  
            , Cmd.none  
            )  
  
        SelectNote id ->  
            ( { model | selectedNoteId = id }, Cmd.none )  
  
        UpdateSelectedNoteBody newText ->  
            case getSelectedNote model of  
                Nothing ->  
                    ( model, Cmd.none )  
  
                Just selectedNote ->  
                    let  
                        updateSelectedNote note =  
                            if note.id == model.selectedNoteId then  
                                { note | body = newText }  
  
                            else  
                                note  
  
                        newNotes =  
                            List.map updateSelectedNote model.notes  
                    in  
                    ( { model | notes = newNotes }  
                    , Task.perform UpdateSelectedNoteTimestamp Time.now  
                    )  
  
        UpdateSelectedNoteTimestamp newTime ->  
            case getSelectedNote model of  
                Nothing ->  
                    ( model, Cmd.none )  
  
                Just selectedNote ->  
                    let  
                        updateSelectedNote note =  
                            if note.id == model.selectedNoteId then  
                                { note | timestamp = Time.posixToMillis newTime }  
  
                            else  
                                note  
  
                        newNotes =  
                            List.map updateSelectedNote model.notes  
                    in  
                    ( { model | notes = newNotes }, Cmd.none )  
  
        ClickNew ->  
            ( model, Task.perform CreateNote Time.now )  
  
        CreateNote newTime ->  
            let  
                newTimestamp =  
                    Time.posixToMillis newTime  
  
                newId =  
                    newTimestamp  
            in  
            ( { model  
                | notes = [ { id = newId, body = "", timestamp = newTimestamp } ] ++ model.notes  
                , selectedNoteId = newId  
              }  
            , Cmd.none  
            )  
  
        ClickDelete ->  
            let  
                newNotes =  
                    List.filter (\note -> note.id /= model.selectedNoteId) model.notes  
  
                firstVisibleNote =  
                    getFirstVisibleNote newNotes model.searchNoteText  
            in  
            case firstVisibleNote of  
                Nothing ->  
                    ( { model | notes = newNotes }, Cmd.none )  
  
                Just availableNote ->  
                    ( { model | notes = newNotes, selectedNoteId = availableNote.id }, Cmd.none )  
  
        InputSearch searchNoteText ->  
            let  
                firstVisibleNote =  
                    getFirstVisibleNote model.notes searchNoteText  
            in  
            case firstVisibleNote of  
                Nothing ->  
                    ( { model | searchNoteText = searchNoteText, selectedNoteId = -1 }, Cmd.none )  
  
                Just availableNote ->  
                    ( { model | searchNoteText = searchNoteText, selectedNoteId = availableNote.id }, Cmd.none )  
  
  
  
-- SUBSCRIPTIONS  
  
  
subscriptions : Model -> Sub Msg  
subscriptions model =  
    Sub.none  
  
  
  
-- VIEW  
  
  
view : Model -> Html Msg  
view model =  
    div [ id "app" ]  
        [ div [ class "toolbar" ]  
            [ button [ class "toolbar-button", onClick ClickNew ] [ text "New" ]  
            , button [ class "toolbar-button", onClick ClickDelete ] [ text "Delete" ]  
            , input [ class "toolbar-search", type_ "text", placeholder "Search...", onInput InputSearch ] []  
            ]  
        , div [ class "note-container" ]  
            [ viewNoteSelectors model  
            , viewNoteEditor model  
            ]  
        ]  
  
  
viewNoteSelectors : Model -> Html Msg  
viewNoteSelectors model =  
    div [ class "note-selectors" ]  
        (model.notes  
            |> transformNotes model.searchNoteText  
            |> List.map (\note -> viewNoteSelector note model.selectedNoteId)  
        )  
  
  
viewNoteSelector : Note -> Int -> Html Msg  
viewNoteSelector note selectedNoteId =  
    div [ classList [ ( "note-selector", True ), ( "active", note.id == selectedNoteId ) ], onClick (SelectNote note.id) ]  
        [ p [ class "note-selector-title" ] [ text (formatTitle note.body) ]  
        , p [ class "note-selector-timestamp" ] [ text (formatTimestamp note.timestamp) ]  
        ]  
  
  
viewNoteEditor : Model -> Html Msg  
viewNoteEditor model =  
    case getSelectedNote model of  
        Nothing ->  
            div [ class "note-editor" ] []  
  
        Just selectedNote ->  
            div [ class "note-editor" ]  
                [ p [ class "note-editor-info" ] [ text (formatTimestamp selectedNote.timestamp) ]  
                , textarea [ class "note-editor-input", onInput UpdateSelectedNoteBody, value selectedNote.body ] []  
                ]  
  
  
  
-- HELPERS  
  
  
getFirstVisibleNote : List Note -> String -> Maybe Note  
getFirstVisibleNote notes searchText =  
    notes  
        |> transformNotes searchText  
        |> List.head  
  
  
transformNotes : String -> List Note -> List Note  
transformNotes searchNoteText notes =  
    notes  
        |> List.filter (\note -> String.contains (String.toLower searchNoteText) (String.toLower note.body))  
        |> List.sortBy .timestamp  
        |> List.reverse  
  
  
getSelectedNote : Model -> Maybe Note  
getSelectedNote model =  
    model.notes  
        |> transformNotes model.searchNoteText  
        |> List.filter (\note -> note.id == model.selectedNoteId)  
        |> List.head  
  
  
formatTitle : String -> String  
formatTitle body =  
    let  
        maxLength =  
            20  
  
        length =  
            String.length body  
    in  
    if length > maxLength then  
        String.left (maxLength - 3) body ++ "..."  
  
    else if length == 0 then  
        "New note"  
  
    else  
        body  
  
  
formatTimestamp : Int -> String  
formatTimestamp timestamp =  
    let  
        time =  
            Time.millisToPosix timestamp  
  
        hour =  
            String.fromInt (Time.toHour Time.utc time)  
  
        minute =  
            String.fromInt (Time.toMinute Time.utc time)  
  
        second =  
            String.fromInt (Time.toSecond Time.utc time)  
    in  
    hour ++ ":" ++ minute ++ ":" ++ second ++ " UTC"
```

Let’s examine the changes:

- In the MODEL section, we are adding a new piece of data to keep track of in the model — the `searchNoteText`, which we initialize to an empty string.
- In the UPDATE section, the `Msg` now one more type— `InputSearch`, which will be wired up to run whenever the user enters text in the search input. The `update` function with the `InputSearch` case has to filter notes that match the `searchNoteText` as well as select the first visible note. Since I already had logic in the `ClickDelete` case for getting the first visible note, I extracted it into a method called `getFirstVisibleNote` to be DRY and use it in both the `ClickDelete` and `InputSearch` cases.
- In the VIEW section, we add an `onInput InputSearch` attribute to the button, which binds the DOM input event to trigger the `InputSearch` message. We also change the `viewNoteSelectors` function to not use `sortNotes` function and use `transformNotes` instead, which both filters the list of notes as well as sorts them.
- After the VIEW section, we define the new helper functions `getFirstVisibleNote` as well as `transformNotes` (which used to be `sortNotes`).

There may be some further refactoring that can be done to make the use of `transformNotes` more clear. The beauty of Elm is that the compiler works well enough to give you confidence when making such refactors — if it compiles, it works.

The only file we’ve been changing this whole time is **`Notes.elm`**, which you see above. If you’re curious, you can check out the entire project at this [repository](https://github.com/peterxjang/notes-app-elm/tree/80457fb818d5af671fe23740d5f4bc2c418d1e42). (Side note — if you’re not a fan of having all the code in one file, there are definite patterns you can use to organize your code, but they won’t necessarily be the same patterns you may be used to in JavaScript. Check out [Richard Feldman](https://twitter.com/rtfeldman)’s talk on [scaling Elm apps](https://www.youtube.com/watch?v=DoA4Txr4GUs) to get a sense of what I mean!)

### Conclusion

Working with Elm is a pretty large departure from every other framework we looked at. It’s a purely functional language, which means it provides certain guarantees that a JavaScript framework simply can’t. React uses functional patterns like immutability, but since JavaScript is not an immutable language you’re required to work harder to avoid breaking the code. Programming in React often comes with using libraries like Immutable.js for immutable types and Flow for static type checking, essentially bolting on language features onto JavaScript that it doesn’t naturally support. Using Elm makes the whole process much smoother to work with.

The downsides to Elm are the learning curve and dealing with the outside world. For the learning curve, Elm works hard to utilize difficult functional concepts in a way that’s easy to get started. The best way to learn Elm isn’t to try and master the language — it’s to start building apps and learn as you go. The other downside is dealing with the outside world — as we saw, something as simple as getting a timestamp was fairly involved to wire up in Elm. Working with HTTP requests, JSON responses, WYSIWYG editors — these all require a lot more work compared to quick and dirty (but dangerous) JavaScript approaches.

In the [last part](https://medium.com/@peterxjang/comparing-frontend-approaches-part-7-final-thoughts-69cdba516f86) of this series, I’ll wrap up with some final thoughts about each frontend approach and how you might choose the right tool for the job. Stay tuned!
