---
layout: post
title: "What, When, and How to Test in Rails"
date: 2020-04-18
categories:
  - Rails
  - Testing
  - Technical
image: https://www.furnituretest.com/wp-content/uploads/Seating-Durability-Machine.jpg
---

In this post we're going to be looking at some categories of automated tests, Rails testing conventions, terminology, and my personal view on what it makes sense for you to test, and how, if you're new to testing your Rails apps.

As much as I'd like to dive right into the meat and potatoes of testing a Rails app, there are a few introductory topics we have to touch on first. We'll  start by talking about TDD, Rails code generators, generic categories of tests, and generic testing best practices. When I use the word "generic" here, I essentially mean "non-Rails-specific."

<br/>
## Laying some groundwork
<br/>
#### A word about TDD
<br/>

I have to start off my advising any beginner to testing to start off by using TDD. In my opinion, learning about testing is much, much easier if you adopt a test-first / test driven development approach to writing your applications. For starters, you'll write more tests than you would have otherwise, and secondly it will encourage you to write code that is actually making your tests pass. It's slow going to start with, but it encourages good habits and will help you ask the right sort of questions about what your tests actually need to be testing.

<br/>
#### TDD and Rails generators
<br/>

TDD _can_ get a little confusing in Rails due to code generators/scaffolders that simultaneously build a bunch of code as well as the tests for that code - you end up not actually having the option of writing your test _before_ you write your code, and you end up having to write some really big commits. Just do your best when you're in this position - when I've scaffolded a ton of code, my immediate next step is usually to check the tests, and fix any that are broken. Then I commit and move on from there.

<br/>
### Categories of tests, non-Rails-specific
<br/>

In generalized conversations and literature about testing, there are a few different categories of tests that are brought up the most - unit, functional, integration, and system or end-to-end. These categories may seems confusing, and it might seem like they don't fit with Rails code very well. My advice is to not force them to fit - these terms were invented for different languages and frameworks, and make much more sense to people who have experience working in those languages. If you're a dev who has only worked in Rails, you may find yourself getting confused by these terms. "Okay, so model tests are unit tests... and system tests are the ones we do with Capybara... but where do functional tests fit in here? Does Rails even do those? And what category are controller tests in? What about integration tests?" Again, my advice here is to not worry about it. Read stuff about how to test Rails code, but be careful about reading more generic testing literature. Some of it will be useful, but much of it does not apply to us Rails devs, and the vocabulary simply does not map to our code that well. Instead of thinking about unit tests and service tests, it makes more sense to think in terms of test categories that are very easily mapped to our Rails code - model tests for our models, controller tests for our controllers, and system tests for our views.

<br/>
### Generic best practices in testing
<br/>

There are some testing best practices that span across languages and frameworks, and there's one that I'd like to touch on - “Don’t test the framework.” Essentially what this means is that you should be careful not to write tests for things that you as a developer are not responsible for. If you are writing tests in a Rails app, you don't need to check that a line of code like `@post.comments << comment` actually works. This is the responsibility of Rails, not you - it is not your job to make sure that the shovel operator works. Don't test the framework.

Now. This is good advice, but it’s also important to understand that the line between testing your code and testing the framework can get a bit blurry.  When you’re testing a rather simple piece of functionality, don’t let your fear of over-testing paralyze you. It's better to write a superfluous task than omit an important one, and sometimes it's also better to just write a superfluous test than to waste time agonizing over whether it's superfluous. Forgive yourself, write the test, and move on. BUT. You do also have to understand that writing a ton of unnecessary tests is not good - you're doing unnecessary work, adding debt to the system and maybe even obscuring the parts of the application that _are_ high-risk. In short, don't agonize about the possibility of overtesting, but don't ignore it either. I also encourage you to take a look at DHH's [thoughts on the subject](https://signalvnoise.com/posts/3159-testing-like-the-tsa).

Another common idea in testing I'd like to address is the "testing pyramid." This is a graphic that has been around for a long time now, and it basically tries to communicate the ratios you ought to maintain between the number of different types of tests.

<img src="https://blog.octo.com/wp-content/uploads/2018/10/integration-tests.png">

The idea is that since unit tests tend to be faster and system tests tend to be slower, you should have lots of unit tests and just a few system tests. To be honest, I'm not a huge fan of this pyramid. It _is_ true that you need to keep an eye on the speed of your tests suite, and you _can_ technically get more "tests for your buck" if you use lots of unit tests compared to system tests. However, I honestly think that it makes more sense to make your testing decisions based on whether or not a certain test is needed, not by how fast it is. If you can accomplish the same thing with a fast-running test that you can with a slow one, definitely opt for the fast one. But let's not write tons of unneeded cheap tests and skimp on necessary expensive ones just because the triangle said so.

<br/>
## Testing in Rails
<br/>

Okay, that's all that stuff out of the way - let's get down to some brass tacks for Rails-specific testing.

<br/>
### Categories of Tests in a Rails app
<br/>

When you type `rails new` into console, your application will  be given a `test` directory with eight default subdirectories:
- channels
- controllers
- fixtures
- helpers
- integration
- mailers
- models
- system

Lets narrow this list of directories down to the ones that contain actual tests, and also let's get rid of the `channels` and `mailers` directories for now, since you won't need those in every single application you build. We're left with this:
- controllers
- integration
- models
- system

These are the directories that will hold the vast majority of your tests. The `controller` and `model` directories are pretty simple to get your head around - these are simply the directories where we keep tests for our models and our controllers. The `integration` and `system` directories can be trickier to parse apart, though, since they don't map to a specific structure in our codebase. To put the correct tests in these directories, we'll need to know exactly what integration tests are, and what system tests are.

At this point I'm also going to give you a bit of a spoiler and let you know that I do not write any integration tests at all - of the four directories above, that one is usually empty in the applications I write. Here's why. If you look it up, the actual definition of integration testing is testing that is done to make sure that disparate components of an application, once brought together, function well as a whole. However, there are several reasons that this type of testing is less central in modern web development than it was in the past.

With the advent of git,  branching-based workflows, and agile development practices, team members can work on the same codebase simultaneously, iteratively adding small pieces of fully tested functionality, and then just run the test suite to check that 1) their newly-written tests pass and 2) they haven't caused a regression anywhere - i.e. they haven't caused the previously written tests to fail. It is not in vogue nowadays to have multiple teams building out giant chunks of functionality in total isolation from each other over the course of several months, and then frankensteining the pieces together at the end, which is my best understanding of a situation where you would actually need integration tests.

Also nowadays with open source, it’s perfectly normal to have your application largely be made up of packages written by other people. One of the beauties of building your app to rely on dependencies is that you don’t _have_ to test that code - in fact, just like testing your framework, testing your dependencies is actively discouraged.

So that's why I think that integration tests are not something that are as useful as they once were. One thing I do need to mention though, is that you will run into many people out there who write things that they call “integration tests” which are basically system tests, assuming we're using the definition for system tests that I provide below. I have zero desire to argue with anyone about what integration testing is and is not. Just be aware that the definition gets slippery out there. Personally, I almost never put anything in the `integration` directory, and will not be writing about integration tests in this post.

<br/>
### The types of Rails tests you should probably have, and what they're for
<br/>

There are three main types of test that you're probably going to want for any Rails app that you build - model, controller and system. Let's go through these types of tests one at a time and discuss what they're for.

<br/>
#### Model tests
<br/>

You'll want to test that your model instances behave in ways that you expect. To start with, this means testing your validations. If you are adding any validations to your model, you’ll want to check that those validations are really operating as you expect. This means testing both the positive and negative cases- “I should be able to create a user with an email address” and “I should not be able to create a user without an email address.” Do _not_ get in the habit of reflexively checking for every model that “suchandsuch can be created.” At that point, you are very much just testing the framework. If you introduce some complexity to the model instantiation process - a validation, callback, or something similar, then it may be appropriate to add some tests - but not before.

You'll probably also want to test any model methods with appreciable business logic. While you're at it, it also makes sense to test any custom scopes on your model. It's up to you whether you want to test every tiny little helper method in your model, but it's appropriate to err on the side of more tests here; model tests are typically cheap, so there's no reason to be stingy with how many tests you write here. Within reason, of course.

<br/>
#### Controller tests
<br/>

Testing your controllers is actually pretty simple - it just means writing at least one test for each of your controller actions. You’re making sure that things render as they’re supposed to, and that any mutating actions like create, update, or delete, have the appropriate effects. If the controller action is complex, it may be appropriate to have multiple tests for that action. These tests are where you’ll end up catching a lot of errors that would have otherwise been surprising and confusing - the classic “I made a change here and it broke this thing in a totally different part of the app!” The controller tests will tend to catch these, since they’re testing a lot of basic things - that pages are rendering, that requests are completing, etc.

<br/>
#### System tests
<br/>

Let start with a basic definition: a system test simulates an actual user performing an action on the page, and looks at the actual rendered contents of the page to decide whether the test passed or failed. There are a few confusing things about the way that system tests are often described in generic, non-Rails-specific contexts. They’re often described as “end-to-end” tests. To me, this phrase implies that a single system test simulates a user’s entire interaction with the app - a login, a bunch of different actions, and a logout. This is not what end-to-end means. End-to-end means from the top of the stack to the bottom of the stack - controller, view, and model. The idea is, if you simulate a user interacting with the view layer, you are in effect testing the controller layer and model layer as well, since a failure in any of those layers would result in a failure of the entire test. System tests are often cast as the opposite of unit tests, but I reject that notion. A system test only tests one limited “vertical slice” of the application, but it's still only testing a single unit of functionality; it just does it over all three layers of the stack.

There is an idea that is implied by the phrase "end-to-end" and by the testing triangle, which is that system tests should be few, and that each individual test should be long, encompassing a ton of functionality. If you are struggling with a very slow test suite, this might be necessary, but otherwise I would say that shorter tests are generally better. Consider these two system test errors.

`FAILED: User can use billing page`

`FAILED: Checkout button is enabled for user with valid Paypal account`

These two error messages could be describing the same failure, but the second one, being a more granular error message, will clearly make the failure easier to track down. And what are tests for, if not helping us track down problems in our code?

<br/>
## Conclusion
<br/>

The important thing in Rails testing is figuring out what works for you and your use case. You’ll find many people out there speaking very confidently about what to test and when, but you’ll tend to find that, in practice, some tests end up being _much_ more useful than others. Here’s a hint: if you have a certain type of test that _never_ fails, those tests are probably not useful. The whole point of testing is to alert you when something has gone wrong in your app due to a change that you made. If something is highly unlikely to ever break, or get messed up, it probably doesn't need to be tested. Knowing what tests are actually likely to be useful is something that is picked up over time.

Keep in mind when reading about testing that many people use words in completely different ways, and are writing tests in a very different context from yours, so trying to copy-paste their approach will just stress you out. Trust yourself and use your best judgement in building a test suite that works for your application. At the end of the day, what matters is that you can make changes and deploy with more confidence than you otherwise would. Best of luck, and happy testing!
