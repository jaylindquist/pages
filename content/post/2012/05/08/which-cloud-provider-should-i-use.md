+++
menu = "main"
date = "Wed, 09 May 2012 02:49:53 +0000"
title = "Which Cloud Provider Should I Use?"
draft = false
+++

Recently, I read through [Code in the Cloud - Programming Google App Engine](http://www.amazon.com/Code-Cloud-Pragmatic-Programmers-Chu-Carroll/dp/1934356638/ref=sr_1_1?ie=UTF8&amp;qid=1336529446&amp;sr=8-1) and decided to start a new project with cloud computing. Overall, it was quite good with an decent introduction to cloud computing as long as you already know Python and Java with a smattering of server side web development.

Now that I've had a taste of cloud computing, lets get started building something. First, we need to decide where to host the application, and to do that we need to set some requirements. This is just for fun, so price should be exactly free, and it needs to support Java (I'd like to stick Grails or some other Groovy framework, as that is what I am most comfortable with right now), but other options would be nice as well.

What are the popular platforms out there? A quick search gives the following:
* [Windows Azure](https://www.windowsazure.com/): Supports Java, Node.js, .NET and PHP, so there are plenty of options, but it is only free for 90 days.
* [Google App Engine](https://developers.google.com/appengine/): Supports Java, Python and Go. I don't care for Python, but Go is interesting. The fist thing I tried after getting the basics was trying to use Grails, but it looks like the App Engine plugin is currently broken and not being maintained. There is [Gaelyk](http://gaelyk.appspot.com/) however, a Groovy framework specifically for the App Engine. App Engine also gives us free instance hours and backend data stores.
* [Heroku](http://www.heroku.com/): Looks great with support for Java, Ruby, Python, Clojure, Scala and Node.js, but the free account seems very limited with many restrictions.
* [Amazon EC2](http://aws.amazon.com/ec2/): Very flexible with support for many languages and customization, but I'm not really interested in supporting the backend installs myself.
* ... and plenty of others.

Since I've already used Google App Engine and seen some of its limitations, lets start this project with Heroku. Limited storage may not be too big of a setback as this is just for fun, and the list of supported languages really beats the competition in other free cloud offerings.g
