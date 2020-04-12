---
layout: post
title: "Credentials in Rails before and after 5.2"
date: 2020-04-11
categories:
  - Rails
  - Security
  - Technical
image: http://i.imgur.com/OMg2MsV.jpg
---

When building any web application, you will run into a thousand tiny pieces of
sensitive information that you need to keep track of, but that you also can't
commit to source control. This is stuff like passwords to third-party services
that your app needs to know and paths to cloud resources. These bits of information
go by many with slightly different names: secrets, credentials, configurations,
environment variables. There are slight differences in the meanings of these terms, and
we'll discuss some of those differences, but for the most part you can think of
them as the same thing - at least the first three - and you'll be okay.

Rails has changed the way it handles secrets in 5.2. As some teams
migrate their legacy codebases to Rails 5 and beyond and others decided to
stay with earlier versions, it becomes important to clarify the difference between
how creds are done before 5.2, and how they're done after. It's likely
that, as Rails devs who work in all sorts of environments, we will end up dealing
with both systems well into the future.

In this post, we're going to quickly review environment variables as a concept,
then discuss how Rails made use of them leading up to 5.2, and how it has recently
moved beyond them.

<br/>
### A quick review of environment variables
<br/>

Linux gives us environment variables, which is a collection of variables that are globally available, but specific to that shell instance, so they need to be set every time you create a new terminal instance; they do not persist from one terminal instance to another.  They are used for a million different things: from setting the basic settings of our shell to configurations for the applications that we build ourselves.

In terminal, we can access the environment variables of our shell via the ‘env’, `ENV`, or ‘printenv’ command.

In our Rails code, the terminal’s environment variables can be referenced via the `ENV` variable. Unlike the env variable in the terminal, the one in Rails is case sensitive. It must be all upper case.

<br/>
### Rails secrets pre-5.2
<br/>

Pre-5.2, secrets work like this:

There is a config/secrets.yml file. Technically, you could put all of your secrets directly here. But by convention, this file was used more as a liason between your rails code and the linux runtime environment that your app was running in, and which contained the necessary secrets in environment variables. This file does not hold the actual values of your secrets/environment variables; it only holds the _names_ of those secrets, and retrieves the actual values from the ENV. This file _does_ get committed to version control and is visible to anyone with access to the codebase. This file allows for the application to access secrets through the `Rails.application.secrets` interface, instead of having to directly access the ENV from a thousand different places throughout the app. This has the advantage of neatly documenting all of your secrets/configurations without exposing their values.

The actual “secret values” themselves only live in your local ENV, or, if you are using the dotenv gem, they live in your .env file, from where they are loaded into your ENV, and from where they are picked up by the `config/secrets.yml` file.

In production, the ENV is populated by some method or another - it’s usually specific to which platform or cloud provider you are using. But once the ENV is populated, the propagation of the secrets through the system works pretty much identically as in development.

<br/>
### Rails secrets in Rails >= 5.2
<br/>

Starting in Rails 5.2, secrets work like this:

The whole system for how credentials are managed is different. The dependency on linux environment variables is gone. No longer will devs have to set a bunch of variables in their environment. Rather, the way things work now is that the creds are written out in a file (that will not be committed to source control) as part of the Rails app, and that file is then encrypted with a master key. The encrypted file _is_ committed to source control, but the master key is not, and you need the key to be able to read or edit the credentials. So the credentials, through the magic of encryption, are up there on Github for anyone to see, but not readable or editable. This makes dealing with secrets between environments much easier, because instead of having to deal with a bunch of secrets, you just have to deal with one master key, stored in `config/master.key`.
