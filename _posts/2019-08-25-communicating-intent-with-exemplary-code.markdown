---
layout: post
title: "Communicating Intent with Exemplary Code"
date: 2019-08-25
categories:
  - Best_Practices
  - Rails
  - Technical
description:
image: https://images-wixmp-ed30a86b8c4ca887773594c2.wixmp.com/f/0d783665-8f74-4670-88e5-b0f7a57554a3/ddeu2xb-48bc241f-9176-493a-abbb-97a562134ba4.jpg?token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1cm46YXBwOjdlMGQxODg5ODIyNjQzNzNhNWYwZDQxNWVhMGQyNmUwIiwiaXNzIjoidXJuOmFwcDo3ZTBkMTg4OTgyMjY0MzczYTVmMGQ0MTVlYTBkMjZlMCIsIm9iaiI6W1t7InBhdGgiOiJcL2ZcLzBkNzgzNjY1LThmNzQtNDY3MC04OGU1LWIwZjdhNTc1NTRhM1wvZGRldTJ4Yi00OGJjMjQxZi05MTc2LTQ5M2EtYWJiYi05N2E1NjIxMzRiYTQuanBnIn1dXSwiYXVkIjpbInVybjpzZXJ2aWNlOmZpbGUuZG93bmxvYWQiXX0.bF0FocltXQGpzBp1xcnX4Fmn1zTclRprzUTe0Qqb_tU
---
The word "exemplary" gets thrown around in everyday speech as more or less just meaning "good." If we like, we can get
a bit more specific and define it as "a good example of what to do."

With development, we can narrow down our definition even more, to something like "providing a clear template for
exactly _how_ this code ought to be expanded in the future, in terms of design, style, format, organization, what have you."

Let's take classical inheritance as an example. When we create an abstract class in Rails, sometimes we may want to very
clearly communicate to future developers what its child classes ought to look like. For example, maybe we want all subclasses
of class `A` to implement the method `foobar`. We are not at the point yet where we want to try to abstract `foobar` up into the parent class, but we want to make completely sure that all of `A`'s child classes are implementing it.

It's possible that a dev might figure out your intent just by looking at the surrounding classes and noticing that they all have that same method. However, it's likely that at some point, someone will make a child class of `A` that doesn't implement `foobar` at all.

So now let's see what we can do in `A` to make it completely clear what our intentions our for future development:

```
class A

  def foobar
    raise NotImplementedError, "Foobar method was not implemented"
  end

end
```

This is a very interesting error. It gets raised not in the event of "something going wrong with the application", but rather
"something going wrong with the code we're writing."

At this point we have made our intent crystal clear to future developers. _All_ child classes of `A` _must_ have a `foobar` method. Setting this rule in stone sets us up for good things in the future. If the implementations of `foobar` are different in all of the childclasses, but they all get called under the same circumstances, we can eventually move the call of `foobar` up into `A`. This could not be done if there were classes that didn't implement `foobar`.

In general, if you have a good idea about _how_ certain parts of your codebase ought to be expanded in the future, don't be afraid to bake some of those opinions into your classes. Make your code exemplary!
