+++
menu = "main"
date = "Tue, 28 May 2013 12:14:50 +0000"
title = "There is No Such Thing as Two"
draft = false
+++
Years ago, I don't know where, I don't know when, I heard the statement "There is no such thing as two." It was about programming in general, and the definition went something like this:
<ul>
	<li>You can do something zero times.</li>
	<li>You can do something one time.</li>
	<li>You can do something many times.</li>
</ul>

And that's it. You can never do something twice in programming because there is a good chance that once you do something twice, you'll do it a third or fourth time. 

This sentiment comes up in many places like the famous <a href="http://en.m.wikipedia.org/wiki/Don't_repeat_yourself">DRY Principal</a> which uses a bit more formal definition of ensuring all pieces of information have a single source of truth. 

But DRY is usually meant for data. On the processing side, <a href="http://c2.com/xp/OnceAndOnlyOnce.html">Once and Only Once</a> is more appropriate. If you ever see that you've repeated yourself, abstract out the similar functionality and call the common code instead. 

Even though the same idea is hidden within each of the above statements, I still prefer thinking of the number two as an impossible object. 

For one thing, the definition is clearly stated in common words and doesn't try to be academic. 

Secondly, the three bullets have hidden meanings found in other coding best practices. 
<ul>
	<li>You can do something zero times - This is obvious, but drawing it out and stating it explicitly hints at optimization. Any time you refactor code, you should trying to get rid of the parts that are no longer needed. </li>
	<li>You can do something once - There is no need to prematurely optimize and abstract your code. Wait until you know that a new method or class is needed before increasing the complexity of your code base. </li>
	<li>You can do something many times - Here is the heart of "no such thing as two". Once you've done something more than once, you should be able to do it as many times as you want. </li>
</ul>

Lastly, the geekier side of me likes the play on binary. You can only use the digits 0 and 1, but you can represent any number you wish. You'll never see a 2. 

And, as we all know, <a href="http://m.imdb.com/title/tt0584427/quotes?qt=qt0487524">seeing a 2 can be scary</a>.
