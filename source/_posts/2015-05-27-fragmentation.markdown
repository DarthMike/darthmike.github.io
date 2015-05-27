---
layout: post
title: "Fragmentation"
date: 2015-05-27 15:16:50 +0100
comments: true
categories: [ios, apple, tools]
---


As an iOS developer or Apple-centric developer, [wwdc][wwdc] is a time of excitement and news. The yearly event at Apple offers many networking opportunities, but most importantly sets the stage for the next year of development in the company's platform. As this year event is closing in, I wanted to write my thoughts on a related, and often overlooked topic; [fragmentation][fragmentation]

<!-- more -->

# Fragmentation in iOS, really?

Apple is a unique company	in the tech sector because it controls mostly everything that needs to be done to build a consumer electronics product. One of the company's strengths has always been the tight integration between design, hardware and software. 

So one could safely say that, unlike [Android][android], it's main competitor in the mobile space, iOS does not have such level of fragmentation in hardware and software. This has historically been [one quality][frag1] when comparing the Operating Systems as a platform to build end user applications. Nevertheless I think that iOS developers need to cope with a different - but still critical - kind of fragmentation.

# Moving fast

The mobile sector of IT moves very fast. New technologies, APIs, and fast releases are standard. Apple in particular, releases new versions of the iPhone and iPad devices and iOS version every year. A yearly cycle may not seem a short term, but it is short for long-lasting products; the kind of products that are valuable for users and for the people building them.

There are some inconveniences related to moving fast in mobile, in particular in iOS. Let’s see what developers need to cope with every year or in the immediate future.

## Breaking API changes

You may expect APIs to be deprecated with every iOS version, you can expect replacements to be announced for APIs your application uses. Or new additions and different behaviour for existing APIs. What it means for your code:

- **Increase of software rot**: This entropy and yearly variation of the platform only increases the rate at which your code, specially UI code, becomes *legacy*.

- **Adopting new APIs takes developer time**: Time that may not be aligned by your product priorities, but time you need to spend to learn and adopt them.

- **Breaking changes**: App developers need to keep support of older iOS versions for some time. Breaking changes, be it API SDK behaviour, mean you potentially need to either rewrite parts of your existing code or maintain different code paths for different OS versions.

## Bugs

All software has bugs, this is a fact that every software engineer is acquainted with very early in his career. But moving fast means you can’t just wait for platform releases to be polished; to keep your application compatible, to keep *the 5 star ratings coming*, you need to support a potentially rough OS release from day (or week) 1. You may even need to provide workarounds in your code to specific bugs in the latest iOS version. 

Again this will lead to more entropy to the code, thus increasing the software rot.

## New OS features

Apple users upgrade their devices fast. This is very good for application developers because you can target the *latest and greatest*, simplify code, and still be quite confident that your product will be available for the majority of the users in the platform.

The flip-side of this is that users expect the latest features, furthermore they expect them in 3rd party applications. Many times the changes in the OS are not noticeable by users, like [autolayout][AutoLayout], [NSURLSesssion][nsurlsession] or 64 bit support. But sometimes those additions are noticeable, like support for TouchID, App Extensions, UIKit Dynamics or many changes that UIKit faces every year. Even if some features are not noticeable by users, they will notice the effect they have in an application behaviour. For example adding support for [background fetch][fetch] requires using NSURLSession and users will notice that content is ready when application is launched. 

As the application marketplace is very crowded, any kind of promotion is an opportunity to stand out from the crowd; new OS features are a very prominent way to get featured by Apple by the time the new OS version is released, and it is often used to promote applications. Apple does this on purpose, it’s on the users benefits that all applications converge to using new OS features, and presents the App Store with as positive and unified.

The problem with all this is that many times adding support for these new features is not aligned with your project needs. But you may feel pressure to do it because of the aforementioned reasons. Again we are adding development time, potentially every year.

## More devices

iOS devices have traditionally been very uniform in terms of screen size, that is, until the trend was broken almost 2 years ago with iPhone 5. Even though the relationship and differences in screen sizes is *sane* (retina vs non-retina, or just height increase), still, there are differences.

Last year apple introduced a [new API called ‘Size classes’][sizes]. This API can be used to provide a more robust and easier way to reuse code, and also to change user interface for different screen sizes or orientations. I would say Apple acknowledged that developers can’t ignore different screen sizes anymore.

I am not considering Apple Watch here, firstly because your application might not need an extension for it, and secondly because you can see it as a different application altogether.

# Swift

If you are mildly interested in mobile, you have heard of Swift, the new programming language introduced by Apple last year. It has been a wild and bumpy road for early adopters, and the language is still in it’s infancy, even though it’s currently [1.2 version][swift].

Developers not adopting the new language now, are slowly facing a kind of technical debt (and knowledge gap) that they will possibly need to pay years from now. Existing projects, if they go down the road of mixed Objective-C and Swift codebases, face adding even more entropy to the code. The impact this has on existing projects can’t be ignored.

# Apples to Apples

Let me be clear about the take of this post; I'm not, in any way, trying to compare Android and iOS. This is not even a fair comparison to begin with. 

In my opinion, fragmentation **is** a problem developers in Apple platforms need to cope with, if they want to work on software projects lasting more than a year. It is just faced in a different way. Every year the development team needs to spend resources to keep up with the changes in the platform, and still keeping support for existing OS versions and devices.

These changes are not bad at all, but increase the workload for your project, and sometimes distract from the real business needs of your product. That said, keeping with the changes is generally satisfying for engineers as this is the bread and butter of our work. And for me personally, the pace of change is one of the reasons why I love mobile (and Apple in particular). 

I can't wait for what Apple has to say on this year's [WWDC][wwdc]!

[wwdc]: https://developer.apple.com/wwdc/
[fragmentation]: https://en.wikipedia.org/wiki/Fragmentation_(programming)
[frag1]: http://bgr.com/2013/12/13/android-fragmentation-vs-ios-fragmentation/
[android]: http://www.android.com/
[sizes]: https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS8.html#//apple_ref/doc/uid/TP40014205-SW44
[nsurlsession]: https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSURLSession_class/index.html
[autolayout]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/Introduction/Introduction.html
[fetch]: https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html#//apple_ref/doc/uid/TP40007072-CH4-SW5
[swift]: https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40001051-CH4-SW3