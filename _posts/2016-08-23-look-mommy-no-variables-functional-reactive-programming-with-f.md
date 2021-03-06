---
layout: post
title: Functional Reactive Programming 101 with Xamarin
date: 2016-08-23 19:37:14.000000000 +12:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories: [UI]
tags: [Android, F#, FRP, FunctionalReactiveProgramming, iOS, Observable, Xamarin]
meta:
  _edit_last: '1'
  mytory_md_path: ''
  mytory_md_text: ''
  mytory_md_mode: url
  mashsb_timestamp: '1589823359'
  mashsb_shares: '0'
  mashsb_jsonshares: '{"total":0,"error":"","facebook_shares":0}'
  mashsb_shorturl: http://www.codingwithsam.com/look-mommy-no-variables-functional-reactive-programming-with-f/
  dsq_thread_id: '6134836174'
author:
  login: sam
  email: willsam100@gmail.com
  display_name: Sam Williams
  first_name: Sam
  last_name: Williams
permalink: "/2016/08/23/look-mommy-no-variables-functional-reactive-programming-with-f/"
---

This short post aims at exploring functional reactive programming using a simple example by a replacing a mutable variable and callbacks/listeners with an event stream.
Creating a standard F# solution Android, Xamarin Studio adds an activity with a button and a mutable counter similar to the following:

```fsharp
type MainActivity () =
  inherit Activity ()

  let mutable count:int = 1

  override this.OnCreate (bundle) =

      base.OnCreate (bundle)

      // Set our view from the "main" layout resource
      this.SetContentView (Resource_Layout.Main)

      // Get our button from the layout resource, and attach an event to it
      let button = this.FindViewById<button>(Resource_Id.myButton)
      button.Click.Add (fun args -> 
          button.Text <- sprintf "%d clicks!" count
          count <- count + 1
      )
```

In F#, it's better to avoid variables so let's work through replacing this. To begin with, we need three functions:
```fsharp
let always x = fun _ -> x

let eventStream = button.Click |> Observable.map (always 1)

let update count = 
  button.Text <- sprintf "%d clicks!" count
```

The first function ```always``` allows us to return a constant value once applied. We then use the always function to setup an event stream from the button. To implement the same behaviour as the original example, we want to increment by 1, so we apply 1 to the always function using the result to map over the event stream produced from the button's click event handler. The last function is rather straight forward; it takes a value and updates the text of the button.

With these building blocks in place, we can write the final function to glue this all together along with a very complex business logic, the + function:
```fsharp
let addStream event = 
  event
      |> Observable.scan (+) 0
      |> Observable.subscribe updateUi
      |> ignore

// make the call
do add update eventStream
```

So there are a few things going on here. We're creating a function that takes in an observable event stream of type int. We pipe the stream into ```scan```, a function that is the same as ```fold``` but for observables (the type signature has the same pattern as fold). Starting from the right, 0 is therefore the starting value, and our complex business logic is the function that is passed in, the + function. Now that the stream has been transformed into what we need, the UI needs to be updated. Subscribing to the stream is all we need along with the ```update``` function we created earlier. With the ```addStream``` function completed, make the call to pass in our event stream we created earlier from the button. 

Hit the run button, tap the button enjoy your button increment without any variables.

So we got rid of the variable, but how do we do this in a cross-platfrom way? Let's give it a crack. First we need to pull out the logic (function) for updating the UI, so the addStream function changes to:
```fsharp

let addStream updateUi event = 
    event
    |> Observable.scan (+) 0
    |> Observable.subscribe updateUi
    |> ignore

do addStream updateUi eventStream
```
This function is now cross platform. Create a PCL, add a reference it and move the code down and update the call to PCL:
```fsharp
do Core.addStream updateUi eventStream
```
Add an iOS project to your solution and and the following to ViewDidLoad function in ViewController.fs:
```fsharp
type ViewController (handle:IntPtr) as this =
  inherit UIViewController (handle)

  override x.ViewDidLoad () =
      base.ViewDidLoad ()

      // Create button and add to UI
      let buttonRect = UIButton.FromType(UIButtonType.RoundedRect)
      buttonRect.Frame <- CoreGraphics.CGRect(nfloat(0.), nfloat(0.), this.View.Frame.Width, this.View.Frame.Height)
      this.View.AddSubview buttonRect
      let updateUi count = buttonRect.SetTitle (sprintf "%d clicks!" count, UIControlState.Normal)
      let eventStream = buttonRect.TouchUpInside  |> Observable.map (always 1)

      // make the call to the PCL
      do Core.addStream updateUi eventStream
```

With that added, hit the run button and see the counter working on iOS!
Awesome, we have the solution working on both Android and iOS with some shared code, but can we do any better. As you might have seen, there was some duplicated code. Let's push that down, so we know both platforms will remain consistent. The first is the always function, copy it to the PCL and remove from both platforms. The second is moving the creation of the event stream. This can be added to the start of the addStream function so the event stream function now looks like this:
```fsharp
//In Core
let always x = fun _ -> x

let addStream updateUi event = 
    event
    |> Observable.map (always 1)
    |> Observable.scan (+) 0
    |> Observable.subscribe updateUi
    |> ignore

//Android Activity
do Core.addStream updateUi button.Click

// iOS ViewController
do Core.addStream updateUi button.TouchUpInside
```
Awesome build and run to check everything works, or alternatively, write a unit test for the addStream function. The addStream function still does a few things, so these could be refactored out into the PCL to make things easier i.e. the + function could be parameter to the function along with the ignition state.
As always, the completed code with be  <a href="https://github.com/willsam100/FRP/">available on Github</a>.
For a more complex example check out <a href="https://fsharpforfunandprofit.com/posts/concurrency-reactive/">this</a>
