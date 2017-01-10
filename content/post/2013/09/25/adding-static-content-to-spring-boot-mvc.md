+++
 
date = "Wed, 25 Sep 2013 21:46:54 +0000"
title = "Adding Static Content To Spring Boot MVC"
draft = false
 
+++
As I learn new frameworks, I often get a good idea stuck in my head that I really want to implement. Often, the idea is overly ambitious for my knowledge of the tool, but sometimes it hits the sweet spot between "That should be pretty easy to implement" and "I really don't want to dig into the documentation to figure this out".

Using a tool that is rather young, like Spring Boot, makes me dread the learning process even more as they often don't have the documentation I need in place, so instead I need to dig through source code and examples that look related.

My plan was to add static content to a Spring Boot web site. I've seen the dynamic content via Spring `@Controllers`, but I still need to add images and CSS if I want a decent site.

So, my journey began at Spring Boot's GitHub repo, which has a large number of undocumented <a href="https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples" title="Spring Boot Samples">examples</a>. My answer, of course, lay beneath the folders of the well named <a href="https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples/spring-boot-sample-web-static">spring-boot-sample-web-static</a> example, and like all other Spring Boot defaults, it was an obvious solution.

Add the static files to `src/main/webapp`. Where else would you put it?

My own example is now on <a href="https://github.com/jaylindquist/spring-boot-service-and-static" title="Spring Boot Service with Static Content">GitHub</a> which combines the Ember.js template project to the Spring Boot REST Template project.

The last step we need to create a fully functional application is to modify the Ember.js template to pull data from the REST services.
