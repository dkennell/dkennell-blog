---
layout: post
title: "Why 'synchronous' and 'asynchronous' can be misleading terms"
date: 2020-05-23
categories:
  - Technical
image: https://www.si.com/.image/ar_1:1%2Cc_fill%2Ccs_srgb%2Cfl_progressive%2Cq_auto:good%2Cw_1200/MTY4MTY5NDcwMjQ0NzU4ODAx/russia-synchronized-swimming-2016-rio-olympicsjpg.jpg
---

One of the first words aspiring developers run into when getting into the game is "asychronous." 
Asychronicity can be a tough concept to get your head around, but like many things in the industry, 
it's made much harder than it needs to be. The reason asynchronicity can be a tough concept for 
juniors to absorb is that, in development, the words "synchronous" and "asynchronous" do not mean 
the same thing as they do in the rest of the world.

<br/>
### What does "synchronous" mean outside of web development?
<br/>

It means "happening at the same time."

Like the end state of [these metronomes.](https://www.youtube.com/watch?v=5v5eBf2KwF8) Synchronous.

Pretty simple, right?

The word "sychronized" means pretty much the same thing. Synchronized, sychronous, in sync... 
They all mean the same thing. In fact, the prefix "sync" means "together." So pretty cleary, it
must mean the same thing in development, right?

<img src="https://i.pinimg.com/originals/0e/5f/01/0e5f01db54695e28cdc8e9090f465033.jpg" alt="A kitty saying haha no">

I'm afraid not.

<br/>
### What does "synchronous" mean in web development?
<br/>

In development, I'm so sorry to tell you, things are different. Here, synchronous does not mean
"at the same time," but rather "one at a time, in order." In development, the term "synchronous" is
essentially a synonym for "sequential." Just like java and javascript are not the same, synchronous
in the world of development and synchronous in the outside world are _not_ the same thing. They
are different, and it's important to make that distinction explicit.

Now. In many tutorials about asynchronicity, this fact tends to get swept under the rug. My best guess as to  the reason for this is that we're trying to convince each other that we're using that word for a good reason besides the fact that, at some point, some dev somewhere very clearly fucked things up but now it's too late to change things and this is just the word we all have to use.

But in my opinion it's important to talk about, and to understand, for a couple of reasons. One, 
it's necessary for understanding async programming, and two, it's a good reminder to think hard 
about how you name things, because you could be starting a convention that ripples outward for decades to come.

<br/>
### So what does "asynchronous" mean?
<br/>

Outside of web development, you would expect "asynchronous" to mean "not at the same time." And this
is essentially what it _does_ mean. When people talk about "asynchronous communication," they're 
talking about things like email - communication media where people don't need to be using it at the
same time for it to work. A phone call, conversely, would be a _synchronous_ form of communication.

Now remember - in web development, synchronous mean "sequential." So in development, asynchronous
means "not sequential."

Let's imagine that we are writing some code to perform the following tasks:
```
1. Create an account for the user
2. Send a confirmation email to the user
3. Thank the user for signing up
```
Some code that is operating sychronously would basically just do this:
```
1. Create account
2. Send email
3. Thank user
```
However, let's say that the email takes a while to send, and that we don't want to wait for it to finish before we display the thank you message. We can do this:
```
1. Create an account for the use
2. Start the process of send the email, but don't wait for the task
   to finish. Just let it run in the background
3. Thank the user for signing up
4. Email-sending process finishes
```
This is a super common use case for asynchronous activity. 

One thing you might notice about the last example is that at a certain point in the program, steps 
two and three are running at the same time. So somewhat perversely, our asynchronous code is running
in a way that is literally the opposite of the non-development definition of asychronous, which as 
we can recall, was along the lines of "not happening at the same time."

<br/>
### Conclusion
<br/>

To some, it may seem a bit nitpicky to have an entire post teasing apart the differences between the
various definitions of synchronicity. But to me, these kind of inconsisencies can be extremely destructive, especially if left unaddressed. According to Bob Martin, the number of programmers doubles about every five years. And almost all of them will, at some point, have to figure out
asynchronicity. "Asynchronous" is a poorly named variable in the codebase that is our industry. It's
a giant piece of technical debt, a convoluted N + 1 query in the education of junior developers. I
propose replacing the terms "synchronous" and "asynchronous" with "sequential" and "asequential."

Now let's enjoy some lovely synchronized swimming. I personally prefer the duets to the team event.

<iframe width="560" height="315" src="https://www.youtube.com/embed/oLWIB3DFM9Q" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>