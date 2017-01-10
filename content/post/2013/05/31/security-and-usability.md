+++
menu = "main"
date = "Fri, 31 May 2013 11:48:40 +0000"
title = "Security and Usability"
draft = false
+++
With respect to my last post, cars are actually quite easy to learn and use despite their extreme complexity. A driver can easily move from one vehicle to the next and comfortable operate it with only a quick look at the controls. This made possible by the fact that each car is trying to solve the same problem within the same limitations of size, road construction and regulations. 

Security frameworks for programmers have a different set of problems that hinder usability greatly. 

They need to solve a great number of security issues: XSS, CSRF, encryption, encoding, file indirection, logging, etc. All these issues are so unrelated that single framework which covers them all would be horrible to learn. 

A framework that specializes in a single solution space such as <a href="https://www.owasp.org/index.php/Category:OWASP_CSRFGuard_Project">CSRFGuard</a> from OWASP can still have usability issues as they are trying to solve a difficult problem after an application has been built. 

What developers need is security to be handled below the application level - within their MVC framework or even the language itself. This is needed as security should not be the job of the application programmer. Apps should not need to be designed around security issues. They should be designed around business requirements and security just comes along for the ride. 

As framework designers, we need to remember that security is a necessary evil that adds no e real product value. It's insurance that you pay up front to prevent possible issues in the future. In fact, security done right will always look like a waste if time - you've prevented attacks but no one will realize it as they will never see the actual threats. 

I would really like to see OWASP and other security minded organizations contribute more security code to popular MVC frameworks like Spring MVC or JSF instead of creating new third party frameworks that can only work once the application exists. 

Both security and car design have large, complex problems to solve and both need increased usability, but security is an always changing field with so many different problems to solve that a common design will never emerge. If we want programmers to secure their applications, then we need to make security "just work". Given to choice between preventing possible attacks at vague times in the future or shipping a new product that will earn the company money, we all know which option will be chosen.
