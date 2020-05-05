---
layout: post
title: "Sending a react-native voice recording to AWS"
date: 2020-01-26
categories:
  - React-Native
  - Javascript
  - Technical
  - AWS-Amplify
  - AWS
image: https://s29843.pcdn.co/blog/wp-content/uploads/sites/2/2018/02/Marketing-Campaign_Blog-Images_Best-Microphone-e1540233042276-768x571.jpg
---
So I've had this idea for a little while that I'd like to work on voice
recognition for mobile apps. You have to walk before you can run, though, so
right now my focus is just on getting comfortable with mobile, and audio on
mobile. React-native has been the natural move for me to get stared in mobile,
and I've been slowly getting more comfortable with it over time. I started by
building a [very scary laser gun](https://github.com/dkennell/react-native-deadly-laser-gun)
just as a proof of concept that I can play sounds from a phone. This most recent
project has been me moving on to try and actually record a user's voice and send
it to a server.

Let's start by discussing the stack I've ended up using here. When it comes to
just basic development of a react-native app, I've decided to use Expo. I chose
that for a few different reasons, many of them intertwined with the fact that
I am working with Audio. First of all, it's really really sexy. I mean, geeze -
you just point your phone's camera at a QR code and blam, you're looking at the
app on your device. Kinda gives me chills, lol. Beyond that, though, the driving force
behind choosing Expo is the Expo SDK - essentially a giant extension of the
react-native ecosystem, made available in a really beautifully and thoughtfully
documented API. The Audio module alone pretty much sealed the deal. The alternative
was to try and struggle with some neglected npm package built by some random person
until I ended up pulling my eyeballs out of my skull in frustration. It didn't
take long to basically accept that the Expo Audio API was the best thing in town,
and I was willing to make the whole project an Expo project in order to use it.

Besides just recording a user's voice, my goal with this project was also to figure
out how to send the generated audio file to a server somewhere. To be honest,
I've never really dealt with assets in my apps in a very conscious way before this. When I
went through my web development bootcamp, we never actually used asset servers -
we just linked to images that were already hosted somewhere on the web, or we
just included the actual image file in an assets directory in the application
codebase. I remember messing around with Paperclip, the file attachment library
for Rails, and somehow getting it to work. It wasn't until I started actually
working in the field and saw things like S3 that the idea of asset servers really
became clear to me. So now in present day, now when assets servers are less of a
fuzzy concept to me, I've decided that I want to be able to send audio recordings
to an asset server. I did some digging and learned that when it comes to mobile,
AWS provides a service specifically for the development of backends for mobile
applications (the phrase 'backend' here includes asset management). The service is
called [AWS Amplify](https://aws.amazon.com/amplify/) and it's a Backend-as-a-Service (BaaS).
It's free to mess around with and it provides your app with a CLI and API that
can be used to, among other things, send files to your Amazon S3 bucket.

Once you go through the basic setup for Amplify using the The Amplify CLI, a file
called `aws-exports.js` will be generated in the root directory of your project.
This is essentially a configuration file that tells Amplify which server to send/retrieve
data from. Once that's done, the Amplify npm package gives you access to an module called
`Storage`. It's crazy simple to use. You simply call `.put` on the module
and pass 1) in the filename that you want the file to have, and
2) the actual thing you want stored. I was quickly able to send a textfile
to my S3 bucket. Awesome.

Sending anything beyond that became a challenge, though. I have only the fuzziest
of understandings of how the file system works in iOS, and where things are actually
stored. (My phone is an iPhone so that's all I'm really concerned about right now.)
I actually did a bit of reading about that. Turns out all native iOS applications are
given their own sandbox and their own storage and are not allowed to reach out into
the broader iOS system at all, unless it's through very specific, predetermined,
Apple-built APIs. That's why third-party apps can access your photo roll, for example, but
not your settings. I wasn't immediately sure what to do with that information, but it
was still interesting.

One thing I knew I had to get figured out was my state management. I wanted one button to
start recording and another button to stop recording and upload the audio file. As
much as I would've liked to avoid using state, there was no getting around it. I
needed to have two listeners going who both affected the same `recordingInstance`
object in different ways. I needed to be able to save that object in state.

The Expo Audio API is, as I have mentioned, great. However, it's been a minute since
I've done any heavy React work, and things change fast in that ecosystem. One thing
I learned is that there's been a big move away from class components to functional, to
the point that create-react-app and create-react-native-app [now
make App.js a functional component, not a class-based component](https://www.reddit.com/r/reactjs/comments/blad60/why_react_changed_from_class_component_to/) by default. Ways of managing state have changed
too, with a sharp move away from the  `setState` method available in Class components
in favor of React Hooks and many other methods.

This had me kind of turned around for a while. I honestly am not looking to learn a
completely new way to manage state right now, so I tried to make App.js a class
component, but react-native yelled at me so I created a separate class component and
imported it into App.js and then react-native was happy. Phew. I was able to save my
`recordingInstance` object in state when recording began, and then access the same
object again when the "stop recording" button was pressed.

Where I ran into some more trickiness, though, was figuring out how to get the recording
to S3. What exactly was I needing to send? The actual `Audio.Recording` object?
I found a method called `createNewLoadedSoundAsync` that changed it from an
`Audio.Recording` object into an `Audio.Sound` object, which I could actually play!
So now when I pressed stop recording, my phone would play back the sound to me. Sick.

But it still wasn't an upload. I tried sending the `Audio.Sound` object to my S3
bucket in a bunch of different ways, but no dice. I thought maybe I had to convert
it into a binary blob or something, and I brought in a ton of different libraries to
try and do that, but still no success. All I was succeeding in doing was sending
bits of JSON to S3. It was nice that I was sending something through, but it wasn't
an actual audio file, just weird little JSON files. Eventually I noted in the docs
for the API that the `Audio.Recording` object had a `.getURI` method, which actually
gives you the path to where exactly the audio file is saved in (presumably) that little
sandbox that all native apps are provided with. So I backtracked a bit and focused
on the Recording object instead of the Sound object. Logging out `myRecording.getURI()`, I got strings like
this: `Recording URI:  file:///var/mobile/Containers/Data/Application/F43A65C4-1C71-4158-8374-B7CCD2B9796F/Library/Caches/ExponentExperienceData/%2540anonymous%252FmyAmplifyProject-cb2bc185-2ec8-4e28-beef-30fdb6579c90/AV/recording-B65A4384-F721-445C-AF92-5EEB4697EFA8.caf`

I tried passing in the url itself as the argument for the resource I wanted to send
to S3, but that didn't work. Googling around, I finally found someone who was complaining
about trying to do something similar and not having any success - what they were doing,
though, was calling `fetch` with the URI, awaiting the result, saving it in a variable,
and passing _that_ as the second argument of `Storage.put`. Oh yeeaaahhh... you can
use `fetch` to retrieve resources from urls, even local ones.
I'm so used to using `fetch` to do AJAX, I always forget you can use it to deal with
local resources as well. So I called fetch on the url, passed in the result and BLAM!
Working audio file on S3! WOOHOO!

So that's where I'm at now. The unceremonious name of the repo, `myAmplifyProject`,
illustrates how little success I expected to have with this my first time through.
We're making forward progress, though! Next, I'll have to see if I can figure out
a more sophisticated way of storing the files in S3, and see if I can retrieve and
play them as well. Eventually, I'd love to get a fully featured cloud-enabled voice
recorder built. That would be a good enough project to feature on my Github profile. Anyway, you
can check out the repro for this recent project [here](https://github.com/dkennell/myAmplifyProject). Thanks for reading!
