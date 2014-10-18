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
So I decided a while ago to version my config files using git, as many other programmers do nowadays. The trick I like to use is to symlink files from my git repo to $HOME, where all these files reside. For example:.gitconfig or.bash_profile. 

I will usually run a script after cloning my env repo. This script will install homebrew for OSX, update ruby to a known version, install gems I use, and symlink from my environment to HOME the config files I use. An example:

```bash
#!/bin/sh

echo '...Installing RVM...'
curl -sSL https://get.rvm.io | bash -s stable

echo '...Installing homebrew...'
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

echo '...Setting up ruby 2.1.1...'
rvm install ruby 2.1.1
rvm use ruby-2.1.1

echo '...Installing ruby essentials...'
gem install cocoapods
gem install bundler
gem install sinatra
gem install nomad-cli

echo '...Installing homebrew essentials...'
brew install ntfs-3g
brew install cmake
brew install pow
pow --install-system
pow --install-local
brew install tree
brew install git bash-completion

echo '...Symlinking env .bash_profile to ~/.bash_profile ...'
rm $HOME/.bash_profile
ln .bash_profile $HOME/.bash_profile

echo '...Symlinking env .bashrc to ~/.bashrc ...'
rm $HOME/.bashrc
ln .bashrc $HOME/.bashrc

echo '...Symlinking env .gitconfig to ~/.gitconfig'
rm $HOME/.gitconfig
ln .gitconfig $HOME/.gitconfig

echo '...Symlinking .gitignore_global to ~/.gitignore_global'
rm $HOME/.gitignore_global
ln .gitignore_global $HOME/.gitignore_global

source .bash_profile
```
#Remember: There is life after origin
Sometimes we are used to git origin to be the repo we push code to, or github/bitbucket for our personal or open source projects. But you can do more. Another trick I use, is having those settings stored in Dropbox as a remote for my settings. The advantage is that I don’t have to remember the remote urls of my public source repo, just reference a local disk url, which is usually ~/Dropbox/reponame. 

You can setup a new remote in dropbox like this:
```bash
cd /path/to/dropbox/
git init —bare myrepo.git
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
So the goal is to improve gradually, because we learn new tools and tweak existing ones. Git can help you with that, and really simple scripts to automate background work go really far. If you make symbolic links to your config files in your repo, you can easily push changes to other machines, and you improve your settings over time without managing where your latest changes are.
<br/>
Spend 20 minutes now to think about how to gradually improve your workflow, and you will be saving hours of time in the long term.
