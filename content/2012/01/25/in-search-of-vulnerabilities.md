+++
menu = "main"
date = "Thu, 26 Jan 2012 02:39:16 +0000"
title = "In Search of Vulnerabilities"
draft = false
+++
Pro tip: When looking for examples of web site attacks, use private browsing mode.

In a search for examples of [CSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery) attacks, I found some lovely vulnerabilities in Facebook and Netflix. What makes for a good example? Well, real-life vulnerabilities in known web sites, or of known companies, preferably with known exposure or losses; examples along the lines of "if this was your bank account..." just don't have the same impact as "so many contacts were stolen from Facebook." Even better is when they show the code for the attack.

The problem with doing this sort of research is that "showing the code" typically means "live demo", and a "live demo" of a security vulnerability means "here's an actual attack". The only difference between an attack and a demo, is that the demo gives you some warning. 

And so it was, that I found an interesting [CSRF attack on Netflix](http://appsecnotes.blogspot.com/2009/01/netflix-csrf-revisited.html) which places a rather embarrassing movie in your queue and moves it to the top. Luckily, it looks like Netflix has addressed this vulnerability, even though I never looked into the attack further. It was at this point that I realized that I was currently logged into Netflix on this computer, and if the attack was still possible, I would have some explaining to do to my wife.
