---
layout: post
title: "All about shells"
date: 2020-07-13
categories:
  - Technical
  - Unix
image: https://i.stack.imgur.com/VSkCU.jpg
---

In this post we will be diving deep on shells: what they are, what they do,
the various types that we will encounter (login, non-login, interactive, 
non-interactive), startup files, and how to use those startup files. We will 
also be discussing ways in which you can experiment with your shell to get a better
understanding of how it's operating.

(I should make it clear that this post is specific to Unix-based systems. I.e. 
MacOS and Linux. Sorry Windows friends, but I don't know your world)

<br/>
## Groundwork
<br/>

Let's start by defining a shell. The broadest definition is "an interface through
which a user interacts with an operating system." If you are a dev who is used to
thinking of shells as an exclusilvely text-based tool, this definition might 
already leave you scratching your head a bit. But yes - technically speaking,
shells _do_ include GUIs. So the GUIs for MacOS, Windows, and Ubuntu are all 
technically shells, and knowing this can prevent some confusion down the line.
There are graphical shells and text-based shells. However, when the phrase shell
is used in development, it is usually assumed that you are talking about a
text-based shell, unless otherwise specified. It's referred to as a "shell" 
because it's the "outermost layer" of your OS - i.e. the interface that a user 
interacts with.

There are many different shellsout there that you can use, but our discussion 
today will mostly focus on two - bash and zsh. bash stands for Bourne Again
Shell. It has that name because it was named sfter an earlier shell called the 
Bourne Shell, so called because it was created by Steven Bourne. bash is simply an update to that shell. Most computers will use bash by default. I personally use
zsh, which is a slightly more fully-featured shell. Comparing some of the
differences between how these shells work will help us to understand shells in 
general a bit better.

Let's also take a second to parse apart terminals, shells, and operating systems.
A terminal is simply a tool that takes input and shows output. Your terminal _runs_
a shell. As we've mentioned, the default shell here will usually be bash. The 
important thing to understand about the shell here is that it's actually a very
lightweight layer between the terminal and the operating system. Most of what it 
does is to take the commands you type in, find the spot on the operating system
where those commands are defined, and run them. So even commands like `cd` and `ls`
are not actually "built-in" parts of the shell - they are part of the OS that 
your shell knows how to find and run. So if you're running zsh on windows and zsh 
on mac, if you run `pwd` on both of those instances, you're actually running two 
completely different programs.

<br/>
## Sub-shells
<br/>

One important thing to know with shells is that you can actually have shells inside
of other shells. If you are using a certain shell, say `bash` or `zsh`, you can 
typically run a nested instance of that shell by simply entering the name of the
shell as input to the shell that you're running. So if you're running bash, simply 
type in `bash` and hit enter. You are now running an instance of bash inside 
another instance of bash. To confirm this, you can enter `echo $SHLVL` into the 
terminal, and it will tell you how deeply nested your shell is, with a return 
value of 1 being your top-level shell and a return value of 3 being a shell nested
two levels deep.

<br/>
## Interactive / Non-interactive shells
<br/>

Now that we have some idea of what shells actually are and what they do, we need
to get a bit of a better understanding of the different ways in which a shell 
can operate.

The first thing to discuss is interactive and non-interactive shells. Simply put,
an interactive shell is a shell that is visible to the user, that takes input and
returns output. A non-interactive shell is one that is never really visible to the
user. When you run a shell script, it doesn't actually run in the shell that you
are currently using. Rather, a temporary non-interactive shell is created
specifically for running that file. That shell is also a sub-shell of the shell 
that you executed the file from.

So in short, if you can actually see a shell, give it input, and receive output,
it is an interactive shell. Otherwise, it's non-interactive.

<br/>
## Login / Non-login shells
<br/>

Another important categorization of shells is whether the shell is a login shell
or a non-login shell. On a theoretical level, the way that login shells are "

So there are a couple of ways to think about the differences between login and
non-login shells.

Let's start with the most literal differences between them.
A login shell is started with certain flags/options that designate that shell 
as a login shell. So we can say that the most basic definition login shell is
"any shell that has been told to be a login shell." In bash, you can tell if you
are in a login shell by entering `echo $0`. If the first character is a dash, you
are in a login shell. In zsh, login shells are designated with an `l` flag. Simply
check to see if the output of `echo $-`, which shows all enabled flags includes a
lowercase L. If it does, you're in a login shell. 

Okay, so what is the actual functional difference between login and non-login 
shells? The main thing is that they use a different set of startup files. This 
is something we'll go deeper into later in the post, where we'll get a better 
understanding of which files are executed when, and what you ought to put in 
them.

The final way we need to contrast login/nonlogin shells is by their intended use.
Generally speaking, a login shells get run whenever you log in to a system,
or change your user on that system, especially if a password is required. Intended
use case doesn't always fit with what actually happens, though, so always remember 
that the main thing that makes a shell a startup shell is the fact that it was 
started as a startup shell. Different operating systems and shells behave
differently, so it's important to confirm what kind of shell you're in and not just
trust the OS/shell to do what you expect.

<br/>
## Specifying a shell as login/nonlogin/interactive/noninteractive
<br/>

In the section after this one, we're going to be going deep on startup files,
which are the files that are run whenever a shell starts up. Different types
of shells result in different startup files being run, so if we want to
experiment with this, we're going to need to know how to explicitely start
shells as login/nonlogin/interactive/noninteractive.

In both bash and zsh, you can start
a nested login/non-login shell by simply including the `--login` flag. You don't
often find yourself in a situation where you need to explicitely set that flag 
to false, but if you do, you can just use `--nologin` instead. Similarly, you 
can open a new "non-interactive" shell with the `--nointeractive` flag. You can
still give the shell input and receive output, so it's clearly still interactive
from a practical perspective, but the system now classifies it as non-interactive,
meaning the startup files will be run as such.

<br/>
## Getting a handle on your shell startup files
<br/>

Shell initialization files or startup files are a series of shell scripts on your
OS that get run, in order, when you open a new shell. There _is_ actually one
script that runs whenver we _close_ a shell, so it might make sense to call these
scripts "shell lifecycle scripts" instead of startup files, but startup files is
the most comonly used term so we're going to stick with that for now.

The most important thing to know right off the bat is that different shells use
different startup files. I didn't realize this at first, and thought that at 
least _some_ of them were always run no matter what shell you use. Not the case.
To actually get some information on what the startup files are and when they're
run, you'll need to go to the official website for your shell of choice and 
look at their docs.

Here is the documentations for the bash startup files: [https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html)

And for zsh: [http://zsh.sourceforge.net/Guide/zshguide02.html#l6](http://zsh.sourceforge.net/Guide/zshguide02.html#l6)

I'm primarily a zsh user so that's what I will be discussing from this point
onward.

There are ten startup files that may or may not run when a new shell is 
started. Here they are, in the order that they're run:

  - /etc/zshenv
  - ~/.zshenv
  - /etc/zprofile
  - ~/.zprofile
  - /etc/zshrc
  - ~/.zshrc
  - /etc/zlogin
  - ~/.zlogin
  - ~/.zlogout
  - /etc/zlogout

You may find that some of these files are not present on your machine. They are 
not all automatically created during the shell install process. If you want them,
you will have to create them yourself. You'll probably find that you don't actually need more than three or four of them, but it can be fun to add them in 
and confirm that they get run when you would expect.

Different combinations of interactive/non interactive shell and login/nonlogin
shell result in different files being run. Refer again to the zsh documentation 
linked above for specifics.

A practice that I recommend to everyone is to put an echo statement in every
one of the startup files that you have, simply stating something like 
`In /etc/zshenv`. That way, whenever you start a new shell, you are notified that
this particular file has just been run. Then you can also experiment with starting
shells with various configurations and checking that the expected files are run. Remember, if you
have more than one user on your machine, the changes you make to the startup files
in one user's home directory won't exist for another user. They will both be 
affected by `/etc/zprofile`, but each will need their on `~/.zprofile`. For that
reason, I recommend including your username in the echo statements that you put
in your home directory, fx something like 
`In ~/.bash_profile for user: davidkennell`.

<br/>
### What to use the various startup files for
<br/>

At this point we have learned how to start a shell as 
interactive/noninteractive/login/nonlogin, we know how to check if a shell is
interactive/noninteractive/login/nonlogin, we know which startup files run when,
and we have put echo statements in those files to confirm that they are run when 
expected.

All that's left now is to understand exactly what _ought_ to go in those various
files. As a dev, you'll end up needing to put lots of stuff into your startup 
files. Aliases, shell functions, modifications to the path, etc. Let's talk about 
what ought to go where.

The first way we ought to divide things up is by global files vs users-specific
ones. There are two main places where startup files live: in `/etc` and in
the home directory. Looking at the list of startup files, you'll quickly notice 
that pretty much every file has a `/etc` version and a home directory version. The
ones in `/etc` are meant for global configurations that effect every user. The
ones in the home directory are just meant to set configurations specific to the
user for that directory.

The `zshenv` files, should you chose to make use of them, are meant for
configurations that set/alter environment variables. They are also the only 
files that will run for every single shell, no matter what type. So if you 
have something that always needs to be run, no matter the "interactiveness"
or "login-i-ness", set it here.

The `zprofile` and `zlogin` files have essentially the same purpose - they are
meant for configurations that you want to make available for all login shells.
Most of the shells you will be using as a dev will be non-login, so these are
probably not files you will alter much. The only reason there are two is because
the `zprofile` files runs before the `zshrc` files, while the `zlogin` files run
after. Different devs have different preferences for when they want their login
shell startup files to run, so zsh decided to include an option for both parties.
Zsh recommends just using one of them and deleting the other.

The `zshrc` files are the ones that we will probably open the most as devs. The
reason for this is that these files contain configurations for interactive shells,
which is the type of shell that we interact with most. If you want to define an
alias that `cd`s you into a frequently used directory this is the spot to do it.
Shell functions go in here. Also anything that configures your shell to look or
act differently should go in here. It doesn't make much sense to put a
configuration for interactive shells somewhere that will affect both interactive
and non-interactive shells, even if it doesn't break anything. If you have shell
functions or aliases that you want to have available for use in shell scripts, this
is not the place for them - shell scripts run in non-interactive shells, so those
configurations should go in the `zshenv` files.

And the `zshlogout` files should contain anything that you want to happen when you
exit a shell. I haven't found much use for them yet, but they're fun to play with.

<br/>
## Conclusion
<br/>

And that's that! While working on websites, we end up downloading tons of different
tools onto our machines, and nervously following their step-by-step tutorials,
adding stuff to our startup files just because they told us to. Once you get the
ideas in this post into your mind, you will be more confident in deciding exactly
what ought to go where, and you will have nicely organized, intentional startup
files, instead of jumbled messy ones from years of just doing stuff to the startup
files that the tutorials told you to. Maybe you've even realized that, by using
different shells, you've been inconsistent in the configs you've been setting for
one shell vs another. That's why your ruby version manager works in `zsh` but not
in `bash`. 

<br/>
## Further reading
<br/>

### Dotfiles
<br/>

After you get a handle on your shell startup files, its worth thinking a bit more
deeply about how you have them organized, and whether it can be done better. Why
not organize them more the way you would organize a codebase? Why not have
totally separate files for shell functions/aliases/environment variables, etc?

If you want some ideas for how this customization could look, I suggest looking up
"dotfiles." This is a term that refers to configuration files on Unix-based
systems, which are often preceded by a dot. The goal of people in the 
dotfile-focused community is to have all of their configurations saved somewhere,
like on a github repo, so that when they start work on a new machine, they can 
easily get up and running with the exact configurations that they prefer. This 
does not just refer to shell configs, but git configs, inline text editor configs,
etc. Try searching 'dotfiles' on github to get some ideas from others on how to
customize your own configs!

<br/>
### Zsh startup files cheat sheet
<br/>

I created these lists to help myself better understand which startup files run when for zsh. Hopefully they'll be useful for you too!

- In order, the zsh files that run for all zsh instances:
  - /etc/zshenv
  - ~/.zshenv

- In order, the zsh files that run for all login shells:
  - /etc/zshenv
  - ~/.zshenv
  - /etc/zprofile
  - ~/.zprofile
  - /etc/zlogin
  - ~/.zlogin
  - ~/.zlogout

- In order, the zsh files that run for all interactive shells:
  - /etc/zshenv
  - ~/.zshenv
  - /etc/zshrc
  - ~/.zshrc

- In order, the zsh files that run for an interactive login shell:
  - /etc/zshenv
  - ~/.zshenv
  - /etc/zprofile
  - ~/.zprofile
  - /etc/zshrc
  - ~/.zshrc
  - /etc/zlogin
  - ~/.zlogin
  - ~/.zlogout
  - /etc/zlogout
  
- In order, the zsh files that run for an interactive non-login shell:
  - /etc/zshenv
  - ~/.zshenv
  - /etc/zshrc
  - ~/.zshrc
  
- In order, the zsh files that run for a non-interactive login shell:
  - /etc/zshenv
  - ~/.zshenv
  - /etc/zprofile
  - ~/.zprofile
  - /etc/zlogin
  - ~/.zlogin
  - ~/.zlogout
  - /etc/zlogout
  
- In order, the zsh files that run for a non-interactive non-login shell:
  - /etc/zshenv
  - ~/.zshenv