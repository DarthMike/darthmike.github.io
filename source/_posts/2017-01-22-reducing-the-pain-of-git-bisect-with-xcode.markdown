---
layout: post
title: "Reducing the pain of git bisect with Xcode"
date: 2017-01-22 11:49:01 +0000
comments: true
categories: tips xcode ios git tools
---

There's times when you need to investigate regressions in your project, and you don't know have any clue as why something is happening. [Git bisect][git-bisect] is the best tool for this cases, but it can be painful to use in non small projects using CocoaPods and Xcode. I want to share what I've been doing to ease the pain.

<!-- more -->

If you never used Git bisect, check this [introduction][bisect-intro]. The git command performs a [binary search][binary-search] across your repository history, starting from two known commits you provide to the algorithm: 'Good' and 'Bad'. 'Bad' will usually be your last commit, and 'Good' will be a commit back in the history of your repository, when you know the code was working as expected. For every step of the search you need to tell git if a commit is good or bad, either manually or automatically by running a script.

# Pain points

When we enter the realm of iOS development, there's a some factors that make bisect tedious to use.

If you use CocoaPods, and if you don't check in the 'Pods' directory in source control, every time you change branch or bisect selects a new commit, you might need to run `pod install` in order to have all dependencies available and compile correctly.

If you are searching for a regression, chances are you don't have an automated test suite, so you'll need to run your app manually and reproduce the issue you are searching for. There will be cases when you're dealing with a bug that needs an app reinstall or needs to run on device to reproduce. To top all this, Xcode will start the long indexing process every time there's changes to the sources due to change of commit, so your computer will start doing lot of work.

All of these nuances makes the process of searching for issues using bisect very tedious and not so 'magical' as it is supposed to be. I've nevertheless used bisect many times to save time otherwise wasted reading code and navigating breakpoints during long debugging sessions. You just need some patience and the will to automate some stuff.

# Reducing the pain

## Automate pod install

If you don't check in the 'Pods' directory in source control, you might want to run `pod install` after every bisect step, to refresh all dependencies. Make use of git's `post-checkout` [hook][git-hooks] and just perform the operation automatically. I like to close Xcode while updating pods so it doesn't turn crazy and slows down computer even more than usual:

```bash
#!/bin/sh
# Script for .git/hooks/post-checkout
set -e
osascript -e 'quit app "Xcode"'
pod install
open -a Xcode
```
After you say 'good' or 'bad', bisect chooses another commit, and automatically close Xcode, update dependencies and open Xcode again. You can be more sophisticated and open the current project directly. See this [example][zsh-xcode].

## Automate build and run

As I've mentioned before, if you end up doing a bisect in the codebase you don't have unit tests covering the issue, or any other kind of regression suite covering the issue. You'll need to manually run the project and reproduce the bug. You can still automate the building and running, so you just need to wait to reproduce, and tell git if the commit was good or bad.

Combine `xcodebuild` with [ios-sim][ios-sim] in a `post-checkout` script:

```bash
#!/bin/sh
set -e
xcodebuild -arch x86_64 -sdk iphonesimulator10.2 -derivedDataPath ".build" -scheme myScheme

ios-sim launch --devicetypeid com.apple.CoreSimulator.SimDeviceType.iPhone-6-Plus .build/Build/Products/Debug-iphonesimulator/myapp.app
```

Here we force a simulator-only build and run it straight away so you can do your painful manual testing. You can combine the update of dependencies to just leave reproducing the issue and feedback as manual steps in the process.

## Repository discipline

In order to be able to bisect faster and achieve meaningful results there's some practices to follow in your commits:

- **Granular commits are better**: Smaller commits will make results of bisect more useful as the commit will have small changes, and will be easier to determine the source of the problem.
- **Ensure every commit compiles**: It will make a potential bisect easier by not struggling with commits that need to be fixed before tested
- **Check in CocoaPods**: There's many [advantages some disadvantages][pods-in-repo] of keeping Pods in the repository. And one advantage will be the code compiles as-is without need of fetching dependencies.
- **Write meaningful commits**: This should be general practice in the team, but a meaningful commit might tell you what went wrong without even looking at the code, and will help teammates understand your changes.

# Use bisect

Everybody makes mistakes, and there will be the time when you'll be the one tasked to find out why something is not working, with no clue whatsoever of why. This is the time to think about using `git bisect`. It can be tedious under iOS projects, but you can ease the pain by automating some bits of the process and some patience.

And if you find finally find a commit of your 1-month younger self, you'll thank yourself if you wrote a meaningful commit message.

[git-bisect]: https://git-scm.com/docs/git-bisect
[git-hooks]: https://git-scm.com/docs/githooks
[binary-search]: https://en.wikipedia.org/wiki/Binary_search_algorithm
[bisect-intro]: https://git-scm.com/book/en/v2/Git-Tools-Debugging-with-Git
[zsh-xcode]: https://github.com/robbyrussell/oh-my-zsh/blob/57fcee0f1c520a7c5e3aa5e2bde974154cdaf0c3/plugins/xcode/xcode.plugin.zsh
[pods-in-repo]: https://www.dzombak.com/blog/2014/03/including-pods-in-source-control.html
[fastlane]: https://fastlane.tools/
[ios-sim]: https://github.com/phonegap/ios-sim
