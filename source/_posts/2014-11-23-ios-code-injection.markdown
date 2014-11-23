---
layout: post
title: iOS Code Injection
date: 2014-11-23 12:21:36 +0000
comments: true
categories: [tools, productivity]
---
Write code, compile, **wait**, navigate to screen in application. Fix problem, recompile **wait**. Oh it was not fixed, start again... Does it sound familiar? For compiled languages there seems to be no workaround this bottleneck. Even though this is true, there are situations where we can reduce the waiting time, increasing our productivity and happiness. Enter code injection.
<br/>
<!-- more -->
#The problem
When a program is launched by the OS, the code is read from the binary file and loaded in volatile memory and it is then executed by the CPU. When that program is written in a compiled language, the classic approach to update the code is to kill it, recompile it, and load it again from scratch. This guarantees a fresh start and ensures you are running your *'bleeding edge'* version.

The problem with this approach is that recompiling and relaunching the program can take a small, but not insignificant, amount of time. One recompile-relaunch cycle may not be a waste of time in itself, but a developer can go through this cycle hundreds of times in a day, so the time sums up considerably. Adding to this problem, if the program itself is an application, then the developer needs to navigate to the section she is working on and interact with the UI manually to check her updates.

###What about iOS apps?

Thinking in the domain of iOS apps, this fact is even worse. *'Typical'* iOS apps are not overly complex, but the process of developing an application
is time consuming and involves hundreds of iterations and small tweaks to a screen. Working in an environment where small iterations evolve a product,
it is not uncommon that a developer needs to change small UI values or needs to do some small logic changes to a small part of an application. Being highly graphical applications, it's faster to develop and 'transform' the code if you can see your changes directly updated on the screen after saving.

#Code Injection

Code Injection is a way of inserting code in a running process without killing it and relaunching it. You still need to recompile the changed files, but if you inject the code you can remove all the waiting time to relaunch, and the time to setup the state of your application, like navigating to a specific screen.

###Available tools
Currently there are two open source tools available:

- [injection for xcode](https://github.com/johnno1962/injectionforxcode)
- [dyci](https://github.com/DyCI/dyci-main)

Both tools take advantage of Objective-C runtime to modify the code while it is running. *Injection for xcode* offers more features, such as some adjustement sliders, and integration with Apportable, or recent (limited) Swift support.

###Tips

So how do I use code injection?

- I prefer to use DyCi: The code and installation is simpler. I just want robust code injection
- Pixel-perfect tweaks: Paired with my favourite pixel-perfecting app: [Uberlayer](https://itunes.apple.com/us/app/uberlayer/id510139938?mt=12)
- [Krzystof Zabłocki's](https://github.com/krzysztofzablocki/KZPlayground) approach using [kicker](https://github.com/alloy/kicker) to inject on save of file, instead of relying on plugin
- Use in debug configuration with cocoapods; with only one line addition to Podfile, it does not even need to change before release

#Conclusion
No more excuse for this:
<br/>
![image](http://imgs.xkcd.com/comics/compiling.png)
<br/>
We have reviewed a very useful tool to avoid long compile-wait-launch-navigate cycles and improve productivity. Code injection is not a silver bullet  of course - is there a silver bullet in programming?

###Good for:

- Tweaking UI values
- Changing existing logic
- Prototyping
- Changing strings
- Different way of debugging

###Not so good for:

- Writing new code and debugging it
- Unit testing
- Changing singletons or long-lived application objects (like services)
- Rewriting lots of code


