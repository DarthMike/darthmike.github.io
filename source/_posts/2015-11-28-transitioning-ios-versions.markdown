---
layout: post
title: "Transitioning iOS versions"
date: 2015-11-28 20:06:25 +0000
comments: true
categories: iOS tips Objective-C Swift
---

Having worked in companies developing their own products, I've faced many times the problem developers encounter when building iOS applications: Your biggest 3rd party dependency - the OS SDK - is changing under you every year. How does your team deal with this in a maintainable way?

<!-- more -->

# Long-term projects

Unless you do consulting or freelancing, chances are that you will have to deal repeatedly with evolving code in the same project over the years. Even if you jump from project to project, chances are you land into existing projects where there's some code written some time ago.

It's in longer-term projects that [software rot][rot] happens faster. A successful application will face this earlier than expected, and in the fast-paced market of Mobile applications, every year Apple is renovating and ruthlessly changing the  Operating System where our code lives. It requires non-trivial amount of work to keep up with changes, while still producing code for our users.

I've worked in teams where those questions were often raised:

- How do we keep modernising our code, while maintaining backwards compatibility for our users?
- What techniques can be use to keep moving fast, while still supporting older OS versions?
- How do we approach throwing away old code using deprecated APIs in a big codebase?
- How do we write new code taking into account that we will remove support of an older OS version?

# Fragmentation

[As I wrote before][fragmentation], developers in the Apple ecosystem face a different kind of fragmentation. We're forced to adopt breaking changes every year, or 'die' in the process. Apple forces us to start using new features of the OS, and has a policy of deprecating APIs a lot faster than we like.

A mobile engineering team should embrace change, knowing they'll need to assign some engineering resources to keep up with code changes required to support new and old OS versions. Otherwise the code will rot faster than expected. (Anybody heard about complete rewrite of a feature just written a year ago?).

I'm going to share with you best practices I learned to keep up with OS upgrades in an iOS project.

# Transitioning iOS versions gracefully

I would divide best practices in 3 categories:

- SDK use
- Language: Swift / Objective-C
- Maintenance of previous releases

Let's review them one by one.

## SDK use

### Treat deprecated APIs as errors to resolve

I think we should always treat warnings as errors, and deprecated API use is no exception. If this is not managed around the release of the new OS version, chances are it will take time for the team to keep up with new API. It's best to modernise code to use newer API in the face of deprecations, than leaving warnings as *TODO* markers for later.

### At runtime, check for iOS versions

For long time, Apple's [recommended][version-check] way of checking for API existence has been using `respondsToSelector` or `[SomeClass class]`. Apple introduced availability checks for Swift this year, and I think we should change our practices and check for OS version in Objective-C. There's several pitfalls related to runtime checks and so many variations depending on what you are checking for. I don't see any benefit at all to check for methods, classes etc. Apple has acknowledged the flaws of the previous practices and moved forward with Swift. We should to the same for Objective-C code.

Starting with iOS 8 you can use `NSProcessInfo` to accurately fetch the OS version your app is running on. Use it in your macros, and never again check for API availability for `NSClassFromString`, `respondsToSelector` etc.

### ObjC: Mark legacy code paths with macros

This goes in line with the previous recommendation. The problem with maintaining or having exceptions in the code for older versions of the OS, is that in the future that code will be dead, as your team will eventually drop support of that OS version. Say you still support iOS 7 today, and you need to implement use of newer API to convert points from one view coordinate system to another. In iOS8 a new API was introduced, and the previous one was not (yet) deprecated. You might want to migrate to use it when it is available. In swift, you can use *availability conditions* to check at compile-time:

```swift
if #available(iOS 8, *, *) {
  self.view.convertPoint(.Zero, toCoordinateSpace:anotherView)
} else {
  self.view.convertPoint(CGPointZero, toView:anotherView)
}

```

When the time comes to drop iOS8, possibly next year, you'll just search the whole project for `#available(iOS8` checks, and remove the old branch, and removing the check. This cleans the code and clears it of old checks.

You can do the same with Objective-C, defining a function or macro that will determine the OS version at runtime. Furthermore, there may be times where you need to introduce a workaround in the code, related to an older OS version. It is useful to use the same macro to 'mark' the code so when you stop supporting the older OS version, you just need to check for that marker. For example:

```objc
//Assume you have a macro that determines OS version, like MQG_OSVERSION_IOS7
//Mark the code with the macro, doing nothing, just where you are doing a workaround that needs to
//be reviewed when you drop iOS7 support
{
  (void)MQG_OSVERSION_IOS7;
  // Do your stuff, which only is needed under iOS7, but you still do for all OS versions
  // Or implement a delegate method that is not needed for iOS8 or greater, etc
}

```

### Control the number of 3rd party libraries

Any 3rd party library that your project contains adds a cost to your project. If - most likely *when* - Apple breaks compatibility with every OS release, the maintainer may not be as fast as you to adapt to changes. 

Be mindful of this maintenance cost when considering a new library, and always be ready to step in and help the maintainer by pushing changes upstream. If you can't afford to do this, chances are your team will suffer every year when updating all dependencies.

## Objective-C

Chances are your project still has lots of Objective-C code. And it's this code that needs special care, as the team will need to continue living with it, even in the face of Swift.

### Modernise all Objective-C code

If you have such old code still using pre-2.0, please refer to [this][objc-2.0] guide.

There's two important changes to the language since Objective-C 2.0, introduced in 2015; That is the addition of [nullability annotations][nullability] and [lightweight generics][generics]. 

Be sure to use these features in your new Objective-C code, and change APIs of existing code to annotate it for the use from Swift. It will also make you think about exiting code, and possibly encounter bugs! The most important aspect of doing this is that it will ease using older code from Swift, without the need for a rewrite.

### Rewrites in Swift

The future of Cocoa development is Swift, and any application-level Objective-C code is destined to die sooner rather than later. If you can afford to slowly rewrite part of your application using Swift, just do it. How to organise rewrites is a subject of a whole new article, so I won't give an opinion on this just now.

### Keep code templates up to date

It might seem unimportant, but your team should have up to date code templates. With the recent changes to the language, unfortunately Apple has not spent the time to change templates for Objective-C.  I find very annoying that the default templates for Objective-C don't annotate the code with `NS_ASSUME_NONNULL_BEGIN` and `NS_ASSUME_NONNULL_END` macros. So if you are still writing with that language it's better your team takes control of all templates and use modern ones.

## Swift

Swift is changing a lot, but it is [open source][swift]. You can check the roadmap and be ready for adoption from day one, when the new version is released as part of Apple tools.

Not modernising to newer Swift code is not an option. Mainly because there are breaking changes, so it will incur a cost on your development time, which is hopefully returned in developer happiness for using the language - The current state of tooling in Swift makes the experience sometimes slower, so no win there ;).

To keep up to date to Swift developments, be sure that somebody in the development team is following at least to the [evolution announce][swift-evolution] mailing list, and review the [evolution repository][swift-evolution-repository].

## Maintenance of previous releases

- CI with beta/current/1 version before
- Cocoapods in repository


# Conclusion


[rot]: https://en.wikipedia.org/wiki/Software_rot
[fragmentation]: {{site.url}}/blog/2015/05/27/fragmentation/
[nullability]: {{site.url}}/blog/2015/05/27/fragmentation/
[generics]: {{site.url}}/blog/2015/06/09/adopting-objectivec-generics/
[version-check]: TODO
[transitioning-docs]: TODO
[swift]: http://swift.org
[objc-2.0]: https://developer.apple.com/library/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html
[swift-evolution]: https://lists.swift.org/mailman/listinfo/swift-evolution-announce
[swift-evolution-repository]: https://github.com/apple/swift-evolution