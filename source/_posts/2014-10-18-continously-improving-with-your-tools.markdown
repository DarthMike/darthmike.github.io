---
layout: post
title: Continuously improving with your tools
date: 2014-10-18 13:17:49 +0100
comments: true
categories: productivity cli git
---

Have you ever setup your tools in half an hour? In my opinion, the key to improving your tools is gradually evolving what you use, and how you use them. Here’s what I think about it.

<!-- more -->
#Continuous improvement
I like to improve and learn incrementally. This means I start using new tools, improve how I use existing ones, and in the 21st century, working in different computers at different times during the day.

So, I usually find myself in situations where I tweak my config files or I add a new tool to my favourites, and then I customise it a bit. You would expect to have all these new settings across different computers. Wouldn’t it be great if we could automate all this?
#Enter git
So I decided a while ago to version my config files using git, as many other programmers do nowadays. I like to keep my dotfiles and configuration files for applications I use in the same repo.

I take the approach to hard link dotfiles to my $HOME from my cloned repository. Regarding links, [others have tried it before](http://stackoverflow.com/questions/11786623/cant-hard-link-the-gitconfig-file) and you can have problems, specially with .gitconfig. I never had any problem with it, because I edit files manually.

#Remember: There is life after origin
Sometimes we are used to git origin to be the repo we push code to, or github/bitbucket for our personal or open source projects. But you can do more. I like having those settings stored in Dropbox as a remote for my settings. The advantage is that I don’t have to remember the remote urls of my public source repo, just reference a local disk url, which is usually ~/Dropbox/reponame.

You can setup a new remote in dropbox like this:
```bash
cd /path/to/dropbox/
git init --bare myrepo.git
```
Then from your local repo:
```bash
git remote add dropbox /path/to/dropbox/myrepo.git
```
That’s it! You can push to it normally:
```bash
git push dropbox master
```
Bonus for push/pull speed, as both repos are local to your machine.
#Conclusion
So the goal is to improve gradually, because we learn new tools and tweak existing ones. Git can help you with that, and you can take [many approaches](http://dotfiles.github.io/) to how you manage your dotiles. Whatever your approach to keeping these settings, it's important to take care of them normally, improve them, tweak them as you use them.
<br/>
Spend 20 minutes now to think about how to gradually improve your workflow, and you will be saving hours of time in the long term.
