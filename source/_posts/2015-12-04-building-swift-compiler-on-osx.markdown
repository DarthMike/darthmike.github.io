---
layout: post
title: "Building Swift on OSX"
date: 2015-12-05 00:38:35 +0000
comments: true
categories: swift tools
---

Swift is finally [open source][swift-org]! This is great because of many reasons, namely the transparency and chances to learn from the people responsible of building the foundational language you work on. I've been checking how easy was to build the runtime, REPL and compiler, and it turns out the team made an excellent job to make the process very easy. Let's see how to do it.

<!-- more -->

If you ever want to use a bleeding edge version of swift to build your application, now you can! Bear in mind that you should only use development versions of the compiler and runtime only when you are sure you need to do this. Generally as an application developer you rarely need to do so. But hey, let's dive in!

The documentation is very good. Again the team has done an excellent job in my opinion, setting up a full open source setup for the years to come. So I encourage you to read at least the [getting started][swift-started] and [readme][swift-readme] first. 

#Downloading a binary development snapshot

If you don't want to build from sources, you can grab the latest snapshot as described on the [swift download page][swift-download]. It's certainly an option if for example you have an application written in Swift, and you want to assess how much things will change ahead of next Xcode release. You can check [Erica Sadun's post][erica-swift-2.2] if you want more details. Thanks [Erica][erica-twitter] for sharing!

#Building from sources

I just needed to install two dependencies on my machine, as stated in the repository: cmake and ninja. Be sure to set this up before starting a build. I'm using homebrew:

```bash
brew install cmake
brew install ninja
```

I chose to use ninja for the first time I would build Swift, as it is the default build system, and I wouldn't need to change configuration parameters for the build script.

Once you've installed the dependencies, clone the required repositories. Bear in mind that the default build script will try to look for more than just the swift repository. As described in the swift [README][swift-readme]. So you'll need to clone several repos:

```bash
 mkdir apple
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

Once all repositories are cloned, you can build from the main swift repository, with the default configuration. The Swift team included a wrapper script to make compilation really easy. That script will be using CMake and ninja to build the actual binaries and libraries. Just use the default configuration (using ninja), to see if you can successfully build with your machine:

```bash
./swift/utils/build-script
```

With the default arguments, you'll find the build artifacts in a 'build' folder so go and explore. To see the version of the newly built swift compiler. So, go one directory upwards, and find the built binaries ready to use:

```bash
cd ..
cd build/Ninja-DebugAssert/swift-macosx-x86_64/bin
./swift --version
#Swift version 2.2-dev (LLVM 46be9ff861, Clang 4deb154edc, Swift c959ce2c83)
#Target: x86_64-apple-macosx10.9
```

**Success!**

# Happy learning!

I quite liked the quote on the [build-script][build-script], where a rationale is presented behind the decision to have a simple script to build the toolchain. 

> While you can invoke CMake directly to build Swift, this tool will save you
time by taking away the mechanical parts of the process, providing you controls
for the important options.

> For all automated build environments, this tool is regarded as *the* *only* way
to build Swift.  This is not a technical limitation of the Swift build system.
It is a policy decision aimed at making the builds uniform across all
environments and easily reproducible by engineers who are not familiar with the
details of the setups of other systems or automated environments

Even somebody with basic knowledge and unexperienced with *compilers* like myself, could get up and running with a new swift build in very little time.

The release of open source code so close to our day to day jobs is a very special thing for us developers. Compiling swift and the tools is very easy, which just shows the high technical leadership and high quality of the open source release by the team at Apple. Congrats! This will be an amazing journey, for all of us.

[swift-org]: http://swift.org
[swift-started]: https://swift.org/getting-started/
[swift-readme]: https://github.com/apple/swift/blob/master/README.md 
[swift-download]: https://swift.org/download/
[erica-swift-2.2]: http://ericasadun.com/2015/12/03/how-to-compile-with-xcode-and-swift-2-2/
[erica-twitter]: https://twitter.com/ericasadun
