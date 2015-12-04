---
layout: post
title: "Building swift compiler on OSX"
date: 2015-12-04 13:44:35 +0000
comments: true
categories: swift tools
---

Swift is finally [open source]! This is great because of many reasons, namely the transparency and chances to learn from the people responsible of building the foundational language you work on. I've been tinkering with how easy was to build the runtime, REPL and compiler, and it turns out the team made an excellent job to make the process very easy. Let's see how to do it:

<!--- more ---->



If you ever want to use a bleeding edge version of swift to build your application, now you can! Bear in mind that you should only use development versions of the compiler and runtime only when you are sure you need to do this. Generally as an application developer you rarely need to do so. But hey, let's dive in!

The documentation is very good. Again the team has done an excellent job in my opinion, setting up a full open source setup for the years to come. So I encourage you to read at least the [getting started] and [swift readme] first. 

#Downloading a pre-build snapshot

If you don't want to build the runtime from sources, you can grab the latest snapshot as describe on the [swift download page][swift-download]. It's certainly an option if for example you have an application written in Swift, and you want to assess how much things will change ahead of next Xcode release. You can check [Erica Sadun's post][erica-swift-2.2] if you want more details. Thanks [Erica][erica-twitter] for sharing!

#Building from sources

At the time of writing I just needed to install two dependencies on my machine, as stated in the repository: cmake and ninja. Be sure to set this up before starting a build.

```bash
brew install cmake
brew install ninja
```
I chose to use ninja for the first time I would build Swift, as it is the default build system, so using default build would be fine.

Once you've installed the dependencies, clone the required repositories. Bear in mind that the default build script will try to look for more than just the swift repository. As described in the swift README:

```bash
 git clone git@github.com:apple/swift.git swift
 git clone git@github.com:apple/swift-llvm.git llvm
 git clone git@github.com:apple/swift-clang.git clang
 git clone git@github.com:apple/swift-lldb.git lldb
 git clone git@github.com:apple/swift-cmark.git cmark
 git clone git@github.com:apple/swift-llbuild.git llbuild
 git clone git@github.com:apple/swift-package-manager.git swiftpm
 git clone git@github.com:apple/swift-corelibs-xctest.git
 git clone git@github.com:apple/swift-corelibs-foundation.git
```

Once all repositories are cloned, you can build from the swift directory, with the default configuration. That will be using CMake and ninja to build the actual binaries and libraries.
```bash
cd swift
utils/build-script
```

With the default arguments, you'll find the built binaries one level up, so go and explore. To see the version of the newly built swift compiler:




[swift-org]: http://swift.org
[swift-started]: https://swift.org/getting-started/
[swift-readme]: https://github.com/apple/swift/blob/master/README.md 
[swift-download]: https://swift.org/download/
[erica-swift-2.2]: http://ericasadun.com/2015/12/03/how-to-compile-with-xcode-and-swift-2-2/
[erica-twitter]: https://twitter.com/ericasadun
