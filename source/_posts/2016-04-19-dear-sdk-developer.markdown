---
layout: post
title: "Dear SDK developer"
date: 2016-04-19 22:46:27 +0100
comments: true
categories: meta tools
---

As an application developer I get to use many third party code to integrate it into my current project. I want to share a checklist of things a user of an SDK really appreciates.

<!-- more -->

# Documentation

This is the most important part of any SDK, be it open source or closed source. Users don't need to understand your code, or learn by themselves how to use your APIs. Maintaining documentation is a big part of maintaining an SDK, and it goes far beyond documenting the code.

# Documentation!

Really, this is very important and lacking on many commercial SDKs. Please include examples, rationale and FAQ.

# Documentation!!

Don't get it? Just some examples of great additional documentation:

- Quick: [Quick]
- RxSwift: [RxSwift]
- YapDatabase: [YapDatabase]

# Please, open source your SDK

There's nothing more annonying than encountering bugs or unexpected behaviour and having to skim through logs, documentation and trial and error tests. This can happen anytime when using unfamiliar APIs, and having the source to understand what goes wrong is the first big benefit of open source SDKs. Furthermore, if you accept contributions from the community, you might get some developers helping with debugging and fixing issues.

Having open issue discussions help with questions and faster communication. A good example is projects on Github. See [an example of Snowplow devs just guiding me over my confusion][snowplow-github-discussion].

Surely your business model is not dependant on you not publishing the source code that interacts with your platform?

# Changelogs

Any developer including third party code in an application is assuming the quality of your code into theirs. They also have the final accountability for the end users, who don't care where a bug or crash is coming from. Good engineering practice will make users of your SDK wary of changes you introduce, so a changelog is very important to let them know what changed.

And please, don't just add *stability improvements*.

# Version migration documentation

More documentation! This is very special and also very important. If you maintain an SDK for long enough you're necessarily going to need to break APIs. It's at this time that users of your SDK will suffer the most, because an application developer doesn't track your development on a day to day basis. It's very crucial for the happiness of your users that you provide good examples of how to transition to new APIs with the least effort possible. I like this migration guide from [Snowplow][snowplow-migration-guide] a lot.

# Your SDK is not the only one

Many examples you can find are very naive in terms of what the host application code contains. Don't expect that the user of the SDK will be using your code exclusively. Compare these two *getting started* examples from very respectable SDKs:

**Facebook**. From their [docs][fbsdk].

```objc
//  AppDelegate.m
#import <FBSDKCoreKit/FBSDKCoreKit.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  [[FBSDKApplicationDelegate sharedInstance] application:application
    didFinishLaunchingWithOptions:launchOptions];
  // Add any custom logic here.
  return YES;
}

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
  BOOL handled = [[FBSDKApplicationDelegate sharedInstance] application:application
    openURL:url
    sourceApplication:sourceApplication
    annotation:annotation
  ];
  // Add any custom logic here.
  return handled;
}
```

**Branch**. From their [docs][branch].
```objc
// Respond to URI scheme links
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation {
    // pass the url to the handle deep link call
    [[Branch getInstance] handleDeepLink:url];

    // do other deep link routing for the Facebook SDK, Pinterest SDK, etc
    return YES;
}

// Respond to Universal Links
- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray *restorableObjects))restorationHandler {
    BOOL handledByBranch = [[Branch getInstance] continueUserActivity:userActivity];

    return handledByBranch;
}
```

Both SDKs provide you with a naive example without explaining what the return results are, and the eventuality of an application doing **more** work than actually using their SDK. Seems a very contrived example but consider that a clever engineer will not copy-paste your example code, but integrate it into their application where it fits best.

[YapDatabase]: https://github.com/yapstudios/YapDatabase/wiki
[RxSwift]: https://github.com/ReactiveX/RxSwift/tree/master/Documentation
[Quick]: https://github.com/Quick/Quick/tree/master/Documentation/en-us
[snowplow-github-discussion]: https://github.com/snowplow/snowplow-objc-tracker/issues/274
[snowplow-migration-guide]: http://snowplowanalytics.com/blog/2016/01/18/snowplow-objective-c-tracker-0.6.0-released/
[fbsdk]: https://developers.facebook.com/docs/ios/getting-started#delegate
[branch]: https://dev.branch.io/getting-started/sdk-integration-guide/guide/ios/#start-a-branch-session
