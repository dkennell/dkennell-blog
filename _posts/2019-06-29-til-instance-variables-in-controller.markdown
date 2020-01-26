---
layout: post
title: "TIL: controller instances"
date: 2019-06-29
categories:
  - TIL
  - Rails
  - Technical
description:
image: https://greatruns.com/wp-content/uploads/2017/09/Gyre-1999-NCMA_611_407_s.jpg
---
I was looking at an instance variable in a controller the other day and it struck me that I had no idea what actual instance it belonged to. With instance variables in a class it's easy - any instance variable that gets initialized from that code belongs to an instance of the class in which it was defined. But what about controllers?

For a little while, I got so turned around on this topic that I thought that maybe variables that start with `@` in controller were not actually instance variables at all, and that the `@` symbol was simply a signifier that the variable ought to be available in the view.

What I've come to understand now is that instance variables in the controller belong - simply enough - to an instance of the controller class.

So even though controllers in Rails are classes, I'd never really thought of them as classes in the same way that I'd thought of model classes as classes. In my mental map of a rails app, a model class was always a thing that gets instantiated several times, but a controller was just a path for the execution point of the code to travel through, between the `routes.rb` and the view.

In my updated view of controller classes, controllers are, just as with models, a thing that gets instantiated. With controllers, this instantiation happens every time a route is accessed that then needs to go through a controller action.

This is where things get interesting with the instance variables. Before now, my understanding of why `@` variables in the controller are available in the view could be summed up as "I dunno, just because." So as it turns out, the thing about instance variables is that they are available _anywhere_ "inside" the the object that they are an instance of. In other words, once we define an instance variable inside of a class, it's available everywhere in that class.

So the creators of rails used this feature of instance variables to their advantage to pass information from the controller to the view - because the execution of your view is all happening _inside_ the execution of your controller action! That's why instance variables are available to us there - the controller does not "hand off" the variable the view - the code in the view is being rendered _as part of_ a method call on an instance of a controller class.

One interesting thing about passing values from controller actions to views this way is that it's not really necessary - there are other ways of doing it. Namely, using locals.

```
render locals: { resource: "Some text"}
```

The only reason, as far as I can tell, that we use instance variables instead is because they use fewer characters and look cleaner than passing locals this way. The result, however, is that instead of simply passing values to the view, what we are actually doing is simply making a variable 100% globally available until that particular controller action completes - and it doesn't complete until you move on to another controller action. In other words, that variable is available in any code that gets run by that controller action, as well as any helpers that get run in the view. It's not good namespacing. At all. And there are [many](https://medium.com/@eric.programmer/removing-the-hack-in-rails-controllers-52396463c40d) who would prefer the rails standard was to use locals instead of instance variables when it comes to the controller.

In short, controllers get instantiated, everything that happens in a view happens as part of the execution of a controller action, instance variables are very powerful, and instance varibles in the controller are controversial. TIL!
