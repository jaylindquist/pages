+++
menu = "main"
date = "Fri, 30 Dec 2011 03:53:48 +0000"
title = "How Embarrassing"
Categories = []
Tags = []
Description = ""
draft = false
+++
Oops. I was hacked.

And only a few months after I was put in charge of application security at work. I'm sure this would inspire confidence with all our developers.

Since I don't actually pay attention to my blog, I had no idea that I was hacked until a month later when I went to look at some photos I uploaded. After some research I found that the attacker managed to infect all PHP files on my site. At this point I contacted my host as my account was locked down. The conversation went something like this (not actual quotes):

> **Me to Support**
> Help, I've been hacked!

> **Support to Me**
> Yes. Yes you have. We told you about this a month ago. Respond to the email our Security Team sent.

> **Me to Support**
> Um, looks like I lost the email, can you send it to me again?

> **Support to Me**
> Just talk to the Security Team, leave me out of this.

To be fair to the host, they most likely did contact me and I just deleted the email. My fault, I admit it. To be fair to me, I get two or three marketing emails from them a week and immediately delete the messages to prevent my inbox from filling up within a month.

I sent an email to their security folks and they gave me the details. Wordpress' TinyMCE install was attacked via a known vulnerability. They asked that I update my applications, which I did once they figured out how to do that.

In short, I failed at one of the basic tenets of security: Keep your infrastructure updated. I let my blog sit idle for too long and failed to upgrade when a new patch was released. Some script kiddie came by and hacked my site, and my host did the right thing by locking everything down until I could respond. Now I'm back up and starting from scratch.
