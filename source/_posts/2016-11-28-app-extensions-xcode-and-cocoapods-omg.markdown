---
layout: post
title: "App extensions, Xcode and Cocoapods, OMG!"
date: 2016-11-28 09:44:54 +0000
comments: true
categories: cocoapods, tools, tips
---


Have you encountered this error when upgrading to the latest CocoaPods?

```bash
'sharedApplication' is unavailable: not available on iOS (App Extension) - Use view controller based solutions where appropriate instead.
```

If yes, continue reading, as you might have encountered same issue as myself. I've recently had to upgrade a project to using Cocoapods 1.1.0. Things stopped compiling, and I had to investigate the root cause of the problem. It has to do with iOS App extensions, unavailable APIs and how fragile our tooling is ;).

<!-- more -->

# TL;DR


- The first cause of error can be fixed by conditionally compiling with a macro. See example [here][example]
- It's better if you define whole classes or API unavailable using `NS_EXTENSION_UNAVAILABLE_IOS`
- If you had this error in a 3rd party library you'll need it to be fixed by the author (see below)
- If you are a library author and need to have different code paths via preprocessor macros, read [this thread](cocoapods-issue), and follow the recommendation to create a separated subspec for an extension target


# Unavailable API for App extensions

Since the introduction of [Application extensions][app-extensions] several years ago, Apple has marked some API as unavailable for these targets. For example, `sharedApplication` from `UIApplication`:

```objc
@property(class, nonatomic, readonly) UIApplication *sharedApplication NS_EXTENSION_UNAVAILABLE_IOS("Use view controller based solutions where appropriate instead.");
```

Apple is using a new macro, `NS_EXTENSION_UNAVAILABLE_IOS` to mark API as unavailable. There's a new setting on Xcode, `APPLICATION_EXTENSION_API_ONLY`, and if set, the code will not compile if it contains a call to `sharedApplication`. This setting is automatically enabled for extension targets so you get the error in your code when you are writing it.

Writing separate code for an App target and and extension target is not an issue. You just don't use the unavailable API in the extension. But what about reusable libraries?

## Libraries using unavailable API for extensions

You might be using several libraries, and some of these might be using unavailable API for extensions. Notably AFNetworking is one of these, check the [sources][afnetworking]. The developer of the library must take care of this in their code, and mark API that is unavailable for extensions because of the usage of restricted system API.

But then, there might be code that is executing a different code path when compiled against an extension, for example [PinCache][pincache]. If this is the case, the library is forcing you to decide between using unsafe API or not using it. You'll need to define the designated macro in your project to achieve this. See an example [here][example].

## Enter CocoaPods

The problem comes with the integration with CocoaPods, as they (correctly) deduplicate targets. This means that you'll compile the library once (say without unavailable API) and link it to your targets. But sometimes you want to compile with usage of unavailable APIs against your main app, and removing unavailable usage against your target. If you want this, you're out of luck as it's not directly supported by Cocoapods.

### What changed?

Since [Cocoapods 1.1.0][1.1.0], they improved integration with App extensions, and the generated project will enable the compilation flag `APPLICATION_EXTENSION_API_ONLY` for libraries linking against an extension target. This is correct, but then you'll start seeing compilation errors that can be a bit puzzling. Bear in mind that the code will not compile even if you don't call the offending API. The compiler just complains that there is code that uses unavailable API.

### Solutions

So you can't compile, you can't just disable all unavailable API for your main App target. What do you do? There is hope! See [this discussion](cocoapods-issue), and you'll see [neonacho][neonacho] suggesting to use a subspec to duplicate the targets. This is a very practical solution, but it requires the library author to modify their podspec. Kudos for the CocoaPods team to offer support on these issues. We're always catching up with Apple after they break (again) Xcode.

Hopefully this writeup will help you find the solution to your problem if you ever face it.

[app-extensions]: https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/index.html
[afnetworking]: https://github.com/AFNetworking/AFNetworking/blob/master/UIKit%2BAFNetworking/AFNetworkActivityIndicatorManager.h#L44
[pincache]: https://github.com/pinterest/PINCache/pull/72
[cocoapods-issue]: https://github.com/CocoaPods/CocoaPods/issues/5373
[neonacho]: https://twitter.com/neonacho
[example]: https://github.com/snowplow/snowplow-objc-tracker/blob/86c1049e960f72966ed61faa8824dbf1a73840f4/Snowplow/OpenIDFA.m#L48-L52
[1.1.0]: http://blog.cocoapods.org/CocoaPods-1.1.0/
