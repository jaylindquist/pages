+++
menu = "main"
date = "Fri, 25 May 2012 01:32:48 +0000"
title = "GLExtension Registry"
draft = false
+++


Well, after that sad Heroku mess, I've given up temporarily on cloud experiments (although, I will probably look into <a title="Cloud Foundry" href="http://www.cloudfoundry.com/">Cloud Foundry</a> once I get interested again). Instead, lets take a look at Spring Batch in order to make OpenGL developers lives a little easier.

First, some history. OpenGL is a lovely, standardized graphics library implemented on most major operating systems. One of the strength of OpenGL is the large partnership with graphics card manufacturers and the abilty to create extensions. There are literally hundreds of extensions that have been created and defined in the <a title="OpenGL Extension Registry" href="http://www.opengl.org/registry/">OpenGL Extension Registry</a>.

Manufacturers come up with new ideas, create an extension and implement it in their drivers. If the extensions become especially useful, other vendors adopt the extension and eventually it becomes part of the specification for OpenGL. This is how programmable hardware in graphics cards started in OpenGL and now is required if the card wants to support the latest standards.

So, the registry is useful for developers as it defines the latest features available and how they are implemented, but the specifications are just sitting online in plain text. Often, extensions build upon other extensions, requiring developers to hunt and peck for the information they need.

This lead me to create the <a title="GLExtensions" href="http://code.google.com/p/glextensions/">OpenGL Extension Wiki</a> on Google Code. The plan was to create a listing of all the extensions in an easily navigable way. Even <a href="http://www.opengl.org/wiki/OpenGL_Extensions">OpenGL admits</a> that "[r]eading an OpenGL extension is somewhat complicated." It doesn't help that they exist as separate text files online. I wanted to create an alternate to the registry that seemed more cohesive, but doing this by hand was painful, so I gave up and the project was left to rot.

Four years later, I was looking through Google's analytics and noticed that the project is actually getting hits. Approximately ten a day - not overwhelming, but more than I would expect as it isn't in a very usable state.

This got me thinking, is there any way to automate the creation of wiki docs? There must be. The specification documents are fairly consistent and somewhat well formatted. The trick will be parsing it into a usable form. After pondering this for a little while, I've come up with the following design:

Spring Batch is an easy to use framework that takes a  lot of the work out of doing repetitive tasks. Specifically, it can be used to drive nearly any repetitive process and inclodes plenty of hooks for logging and error handling.

Once we download all the extension documents, we open each one up, parse it into a usable object representing each of the sections, then write out the document with wiki formatting. Since Google Code stores the wiki in an SVN repo, publishing is as easy as committing the changes.

The difficult part will be parsing and formatting, but that can be dealt with on a step-by-step basis. First, we need to get Spring Batch configured, which is where we'll go next.
