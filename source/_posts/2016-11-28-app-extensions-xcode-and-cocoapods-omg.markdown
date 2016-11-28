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

# Unavailable API for App extensions

Since the introduction of [Application extensions][app-extensions] several years ago, Apple has marked some API as unavailable for these targets. For example, `sharedApplication` from `UIApplication`:

```objc
@property(class, nonatomic, readonly) UIApplication *sharedApplication NS_EXTENSION_UNAVAILABLE_IOS("Use view controller based solutions where appropriate instead.");
```

Apple is using a new macro, `NS_EXTENSION_UNAVAILABLE_IOS` to mark API as unavailable. There's a new setting on Xcode, `APPLICATION_EXTENSION_API_ONLY`, and if set, the code will not compile if it contains a call to `sharedApplication`. This setting is automatically enabled for extension targets so you get the error in your code when you are writing it. But what about libraries?

## Libraries using unavailable API for extensions

You might be using several libraries, and some of these might be using unavailable API for extensions. Notably AFNetworking is one of these, check the [sources][afnetworking]. The developer of the library must take care of this in their code, and mark API that is unavailable for extensions because of the usage of restricted system API.

But then, there might be code that is executing a different code path when compiled against an extension, for example [PinCache][pincache].


[app-extensions]: https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/index.html
[afnetworking]: https://github.com/AFNetworking/AFNetworking/blob/master/UIKit%2BAFNetworking/AFNetworkActivityIndicatorManager.h#L44
[pincache]: https://github.com/pinterest/PINCache/pull/72
