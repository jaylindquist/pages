+++
 
date = "Wed, 26 Jun 2013 11:45:01 +0000"
title = "The Migration Problem"
draft = false
 
+++
Being able to write code for programmers has an interesting problem. What do you do when you want to change the API or how a component works?

Typically, in an object oriented approach, you ensure that you are programming to valid interfaces only and that you have abstracted the problem away enough that any change you make is undetectable. 

You can't rely on that happening, though. Sometimes the API changes so dramatically that programming to the old interfaces is like stuffing a round peg into a square hole. 

Now you have a migration problem. Version 2 of your API is available and is incompatible with Version 1. What do you do ?

You have a couple options:

* You can take Salesforce's approach and keep all your APIs alive, but that means required maintenance on old code and upkeep for any servers you use. 
* You can deprecate and kill off the old API, but you'll find your current users are rather attached to version 1, if only because migrating to version 2 costs time and resources that could be used to make money. 
* You can create some migration helpers to ease developer's life while forcing a migration.


It's the last option that truly speaks to someone writing code for programmers. The slow migration approach will use some rewritten version 1 code that amounts to glue binding the old API to the new API. Developers can then slowly migrate to version 2 while using version 1 in small steps. 

This glue code is temporary and goes against most programming principles. It will also be rather ugly. However, as the owner of the API, if you can take on the ugly task required by the migration, all your API users can now avoid the ugly code they would have had to write to begin with. 

Hopefully, migrating to the glue code is easier for the developers than completely migrating to version 2. Eventually, you need to kill the old API. This can be done once you know everyone is using the glue code. 

Kill off version 1 and then slowly kill off the glue code, which can hopefully be done without your developers needing to do anything beyond flipping a switch. 

There is no way out of the ugly code required by a slow migration. However, as an API owner, you can make everyone else's lives easier by making your life slightly more difficult.
