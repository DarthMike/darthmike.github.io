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

In longer projects [software rot][rot] stays and the team needs to deal with it. A successful application will face this earlier than expected, and in the fast-paced market of mobile applications, every year Apple is renovating and ruthlessly changing the  Operating System where our code lives. It requires non-trivial amount of work to keep up with changes, while still delivering improvements and features for our users.

I've worked in teams where these questions were often raised:

- How do we keep modernising our code, while maintaining backwards compatibility for our users?
- What techniques can be use to keep moving fast, while still supporting older OS versions?
- How do we approach throwing away old code using deprecated APIs in a big codebase?
- How do we write new code taking into account that we will remove support of an older OS version?

## Fragmentation

[As I wrote before][fragmentation], developers in the Apple ecosystem face a different kind of fragmentation. We're forced to adopt breaking changes every year, or 'die' in the process. Apple forces us to start using new features of the OS, and has a policy of deprecating APIs a lot faster than we like. Instead of supporting a lot of older devices and operating systems, we face many breaking changes every year.

A mobile engineering team should embrace change, knowing they'll need to assign some engineering resources to keep up with code changes required to support new and old OS versions. Otherwise the code will rot faster than expected. (Anybody heard about complete rewrite of a feature just written a year ago?).

Apple recommends maintaining support for the current iOS version and the previous one. At the time of this writing this would be iOS9 and iOS8. Even if your business allows for this rule, you're going to have a transitioning period while you update the application code, and still deliver new features and improvements.

I'm going to share with you what in my opinion are best practices to keep up with OS upgrades.

# Transitioning iOS versions gracefully

I would divide best practices in 3 categories.

- SDK use
- Language: Swift / Objective-C
- Maintenance of previous releases

Let's review each of them in order.

## SDK use

### Treat deprecated APIs as errors to resolve

I think we should always treat warnings as errors, and deprecated API use is no exception. If this is not managed around the release of the new OS version, chances are it will take time for the team to keep up with new API. It's best to modernise code to use newer API in the face of deprecations, than leaving warnings as *TODO* markers for later.

### At runtime, check for OS versions

For long time, Apple's [recommended][version-check] way of checking for API existence has been using `respondsToSelector`, `[SomeClass class]`, or `NSClassFromString`. There's several pitfalls related to runtime checks and so many variations depending on what you are checking for. I don't see any benefit at all to check for methods, classes etc. Apple introduced availability checks for Swift this year, acknowledging and improving over the old practice around ObjC's flexibility. I think we should change our practices and check only for OS version in Objective-C.

Starting with iOS 8 you can use `NSProcessInfo` to accurately fetch the OS version your app is running on. Use it in your macros, and never again check for API availability for `NSClassFromString`, `instancesRespondToSelector` etc. I like to use this to 'mark' code, so we can keep removing legacy code paths. See next tip.

### ObjC: Mark legacy code paths with macros

The problem with maintaining or having exceptions in the code for older versions of the OS, is that in the future that code will be dead, as your team will eventually drop support of that OS version. Say you still support iOS 7 today, and you need to implement use of newer API to convert points from one view coordinate system to another. In iOS8 a new API was introduced, and the previous one was not (yet) deprecated. You might want to migrate to use it when it is available. In swift, you can use *availability conditions* to check at compile-time:

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
  // like implementing a delegate method that is not needed for iOS8 or greater, etc
}

```

### Control the number of 3rd party libraries

Any 3rd party library that your project contains adds a cost to your project. If - most likely *when* - Apple breaks compatibility with an OS release, the maintainer may not be as fast as you to adapt to changes.

Be mindful of this cost when considering a new library, and always be ready to step in and help the maintainer by pushing changes upstream. If you can't afford to do this, chances are your team will suffer every year when updating all dependencies.

## Objective-C

Most possibly your project still has lots of Objective-C code. And it's this code that needs special care, as the team will need to continue living with it, even in the face of Swift.

### Modernise all Objective-C code

If you have such old code still using pre-arc code, please refer to [this][arc] guide. If you still have pre-2.0 Objective-C code, Xcode has a tool that will let you modernise it in minutes.

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

### Continous integration

In my experience, I've found that to keep the development speed, the team generally needs to have access to 3 development environments in CI, available for any build:

- Current stable version of Xcode
- Beta version of Xcode
- Previous version of Xcode (temporarily)

Generally all builds should be scheduled to run against the stable version of Xcode. Apple is moving fast, and there generally is one new beta version of Xcode available. That version may or may not have breaking changes to your project.

Sometimes, the team will find it necessary to build with a previous version of Xcode, for example to be able to deploy a hot fix for an application submitted with it.

### Cocoapods in repository

Different teams have different approaches to dependencies. If you use cocoapods, most likely you'll want dependencies checked in your repository project. Even though there's disadvantages, the biggest advantage is that you'll be able to build previous releases out of a tag. This helps obviously for maintenance reasons, but also to be able to do git bisect reliably. Take this into consideration when deciding if your dependencies should be checked in the repository.

# Conclusion

Maintaining software projects is hard, and if you are working with a team with a successful product (you should!), then most likely you'll maintain it over time. I've shared what I think are good practices to maintain code across different iOS versions, with the minimal effort involved. I hope you find it useful.

[rot]: https://en.wikipedia.org/wiki/Software_rot
[fragmentation]: {{site.url}}/blog/2015/05/27/fragmentation/
[nullability]: {{site.url}}/blog/2015/05/27/fragmentation/
[generics]: {{site.url}}/blog/2015/06/09/adopting-objectivec-generics/
[version-check]: https://developer.apple.com/library/ios/documentation/DeveloperTools/Conceptual/cross_development/Using/using.html#//apple_ref/doc/uid/20002000-1114537-BABHHJBC
[swift]: http://swift.org
[arc]: https://developer.apple.com/library/ios/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html https://developer.apple.com/library/ios/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html
[swift-evolution]: https://lists.swift.org/mailman/listinfo/swift-evolution-announce
[swift-evolution-repository]: https://github.com/apple/swift-evolution
