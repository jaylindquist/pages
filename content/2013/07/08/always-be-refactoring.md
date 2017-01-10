+++
menu = "main"
date = "Mon, 08 Jul 2013 16:26:23 +0000"
title = "Always Be Refactoring"
draft = false
+++
If there is one thing you can do to improve your coding skills, it would have to be refactoring existing code.

Start with some old code written by someone who didn't know better (possibly you) - code that doesn't follow existing conventions - and have at it. Preferably, full unit tests exist for you to verify that the code still works after each change. If you don't have the tests then creating them is your first step.

Once you have tests in place, you are free to change anything you want without breaking the interface. The benefits of this are many: if you are unfamiliar with the code base, this will get you up to speed quickly; you are able to try new coding styles without harming existing, outside code; and you now have an island of up to date code that can be built upon.

It isn't <a title="Coding Horror: Don't Reinvent the Wheel, Unless You Plan on Learning More About Wheels" href="http://www.codinghorror.com/blog/2009/02/dont-reinvent-the-wheel-unless-you-plan-on-learning-more-about-wheels.html">reinventing the wheel</a>, but it gets you just as far. You now have a well-defined set of testable requirements with an example implementation that may or may not help you. All your work can happen within the confines of your module or class without affecting others.
