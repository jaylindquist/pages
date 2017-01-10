+++
date = "2017-01-06T20:54:58-06:00"
title = "Shaving Yaks for Docker"
draft = false

+++

So, I want to learn Docker.

Docker has matured enough that it can no longer be ignored. It is only a matter of time before every developer is expected to know and understand it.

So, after a little playing around, I wanted to start documenting it. That is, write about it publically for me to reference in the future. 

That means I need a blog, but I'm sick of Wordpress -- it is too big and too difficult to manage. Can't I just write text and be done instead of needing to log in and format and so on?

I know! I can use GitHub pages to upload static HTML! So, how do I do that? Well, it looks like Jekyll is the way to go. It compiles markdown and you can use git to upload to GitHub.

But, I'm on a new machine -- I have git installed, but I don't have a key for github yet. So, **Step 1**, [generate an SSH key](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).

**Step 2** -- [install Posh Git](https://github.com/dahlbyk/posh-git) to get a nice command prompt and SSH agent for powershell.

OK, I'm all set up to work with GitHub. Now, how do I install Jekyll? [Hugo](https://gohugo.io/) seems like a nice way to get started. I've never heard of it before, how do I install it?

> Using Homebrew?

> `brew update && brew install hugo`

Um, no, I'm on Windows. Seems like the modern development landscape is very Windows-hostile (not an official chocolatey package in sight). But that's a rant for another time. To the [quickstart guide](https://gohugo.io/overview/quickstart/) I go.

Actually, you know what? I'm going to be jumping around a lot of directories to get this working, **Step 3** I should install the [Jump-Location](https://github.com/tkellogg/Jump-Location) module as well.

All right, back to Hugo. Where was I? Right, **Step 4** installed it (it's a binary, so I needed to **Step 5** create my goto `c:\bin` and add it to my path -- and thank god, I figured this was going to be an npm module).

So, **Step 6** I create a post in markdown easily enough (this post in fact), but you can't see it until you pick a theme? OK, **Step 7** pick a theme and see how long that takes...

...

It took about 25 minutes. That includes **Step 8**, configuring the theme. Wait a minute. [Hugo isn't the same as Jekyll](https://www.reddit.com/r/learnprogramming/comments/3dy5w1/jekyll_vs_hugo_best_static_site_generator_out_of/)? All I need is the binary? Yay! I hate installing Ruby crap on Windows (again, another Windows-hostile language / framework / clusterf***, and another rant for another time).

So, now that I have an awesome site, how do I get it onto GitHub? Back to the quickstart guide. I just need to **Step 9** build it with `hugo --theme=...` and **Step 10** push it to Github.

OK it's on GitHub, but I just got an email that my submodules aren't configured correctly. Submodules? I didn't create a submodule. Oh, crap, I cloned the theme into the `themes` folder. I hate submodules, how do I make this just files now?

OK, **Step 11** how do you [remove all traces of a submodule](https://stackoverflow.com/questions/1260748/how-do-i-remove-a-submodule#1260982) again?

> **To remove a submodule you need to:**
>
> 1. Delete the relevant section from the `.gitmodules` file.
> 1. Stage the `.gitmodules` changes `git add .gitmodules`
> 1. Delete the relevant section from `.git/config`.
> 1. Run `git rm --cached path_to_submodule` (no trailing slash).
> 1. Run `rm -rf .git/modules/path_to_submodule`
> 1. Commit `git commit -m "Removed submodule <name>"`
> 1. Delete the now untracked submodule files `rm -rf path_to_submodule`

Oh right, I forgot, git is horrible. **Step 12** do all that. But none of that exists. This isn't a submodule, it's just a directory with a `.git` folder in it. And git can't seem to figure out what's going on.

OK, **Step 13** when git gets confusing, nuke it and start over. Delete all `.git` folders, remove the repo in GitHub and try again.

Great, everything is published and pushed. Going to `https://jaylindquist.github.io/` I see my site, right? No, "Page not found". GitHub pages expects the content to be at the root of the repo, not in a subfolder. I want to version the markdown as well as the build site. 

Hmm, how can I do that? Oh, it's in the Quickstart guide. Create a submodule -- dangit. **Step 14** time to nuke and start over again.

**Step 15** time to start tweaking to get all the paths correct. And once that is done, **Step 16** tweaking the CSS -- because I just can't help myself.

And -- yay -- [everything is working](https://jaylindquist.github.io/) as expected!

So, that's enough for tonight.

Wait.

Wasn't I going to do something with Docker?