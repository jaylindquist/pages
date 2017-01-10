+++
menu = "main"
date = "Thu, 22 Aug 2013 04:25:28 +0000"
title = "Vulnerabilies of Good Intentions"
draft = false
+++
One of the saddest vulnerabilities introduced into applications are those that are created by design. These vulnerabilities are explicitly written into application requirements or have specific user stories. Often, the vulnerability takes on the form of functionality requested by one or more stakeholders.

Consider the following: <a href="http://www.telegraph.co.uk/technology/facebook/8938725/Facebook-privacy-flaw-exposes-Mark-Zuckerberg-photos.html">Facebook privacy flaw exposes Mark Zuckerberg photos</a>. This flaw was created with only good intentions in mind. Facebook prides itself on keeping the site clean and inoffensive. Owners of the site look for any tool that can help weed out the bad photos from the good. Considering the <a href="http://blog.1000memories.com/94-number-of-photos-ever-taken-digital-and-analog-in-shoebox">size of Facebook's image library</a>, I would want help as well.

So, someone had a great idea: we should crowdsource the effort. Some of us can manually look through photos; maybe we can create an automated scanner to tag possibly offensive pictures; but our best bet is our user base. Those who are actually offended will be empowered to report offending pics.

Enter the Reporting feature:

* Alice sees Eve's photo on Facebook. The photo itself may or may not follow Facebook's guidelines, but all that really matters here is that Alice is taken aback.
* Alice clicks the "Report" link to alert Facebook of this deeply offensive image. She'll need to fill out some information about why it is offensive, but it's straight forward and she submits the form.
	
This feature has a few benefits: Facebook staff can be put to better use reviewing possibly offensive images instead of random images; Users get to feel good about themselves that they helped clean up a site they love; repeat offenders can be duly punished to the limits of  Facebook's Terms and Conditions.

However, where there is one offensive picture, there could be many more. An empowered user who has already reported an image has it set in her mind that image reporting is her duty. If Alice saw the rest of Eve's photos, she could review those as well. So, we tweak the code and a new feature is born:Alice sees Eve's photo on Facebook. The photo itself may or may not follow Facebook's guidelines, but all that really matters here is that Alice is taken aback.

* Alice sees Eve's photo on Facebook. The photo itself may or may not follow Facebook's guidelines, but all that really matters here is that Alice is taken aback.
* Alice clicks the "Report" link to alert Facebook of this deeply offensive image. She'll need to fill out some information about why it is offensive, but it's straight forward and she submits the form.
* Facebook show's Alice the rest of Eve's pictures in the hope that other offensive images will be reported.
* Alice selects more offensive images and her duty is done.

This is great functionality: User feel more empowered, as though they are helping the community as partial owners of the site; Facebook staff has even less work to do; repeat offenders are identified quicker than ever before.

However, there is one flaw in the plan that was overlooked. Because any image can be flagged as inappropriate, user avatars can be flagged as well. Avatars are public as they help with identification when searching for friends. So now, we have a way to report anyone's avatar - which is good - but the new feature will then show us the rest of the user's library - which is bad. It is bad because we now have access to anyone's image library, no matter what privacy setting they used, and all we need to do is report the user's avatar.

Finally, this takes us down to the end game: Mark Zuckerberg, CEO and founder of Facebook, holding a dead chicken by the legs. Private photos posted on the internet for all to see. All from functionality and requirements requested by the business. Functionality with only good intentions, but with a security hole one can drive a truck through.

How do we stop this? Requirements and use cases need to include<a href="https://www.owasp.org/index.php/Application_Threat_Modeling"> abuse cases</a> as well. That is, when listing out features and how they will be used, take a moment to think of how the features can be misused as well.

As a malicious user, I want to access private user photos in order to cause harm and mischief. How can I accomplish this?
