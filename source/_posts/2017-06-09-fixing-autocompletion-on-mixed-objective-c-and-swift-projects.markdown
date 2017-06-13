---
layout: post
title: "Fixing autocompletion on mixed Objective-C and Swift projects"
date: 2017-06-09 14:55:27 -0700
comments: true
categories: swift Objective-C tips
---

This year I had the privilege of attending WWDC for the first time. I knew the labs were very important, and I want to share something that made us very happy. We got autocompletion on Swift code working again for our big project at [Peak][peak].


<!-- more -->

# The issue

If you are using Xcode 8 or Xcode 9 (beta 1 as of this writing), when you have a project with mixed Objective-C and Swift, you might encounters problems with autocompletion inside the Swift code.

We've had this intermitently and most recent IDE versions made work very difficult as we would not have any autocompletion whatsoever for our Swift code.

Our gut feeling was that the bridging header had some classes that were causing SourceKit to fail, but we couldn't debug the issue nor provide good feedback for Apple to fix it. Our chance came while visiting the labs at the conference, and a very helpful and patient engineer worked with our project to undertand what was wrong.

# Bridging header imports

It turns out that if you import libraries without the 

# How to debug

# Conclusion


The changes were minimal and the whole team has a functional IDE once more. Apple is very helpful at the WWDC time and it was very good for this (and many other questions) to be answered. If you attend the conference anytime, be sure to bring your questions, code, and issues to talk directly with Apple engineers.

I hope this helps anybody that is asking 'why my autocompletion broke in Xcode 8 or Xcode 9'. This might be cause of the issue, and you can actually use the debugging menus to help Apple or figure out what is wrong yourself.

[peak]: http://peak.net
