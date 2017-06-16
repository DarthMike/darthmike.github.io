---
layout: post
title: "Fixing autocompletion on mixed Objective-C and Swift projects"
date: 2017-06-16 23:55:27 +0100
comments: true
categories: swift Objective-C tips xcode
---

This year I had the privilege of attending WWDC for the first time. I knew the labs were very important, and I want to share a resolution to fix a problem for our big project at [Peak][peak]: Autocompletion on Swift code was not working most of the time in the IDE, and with the help of an Apple engineer in the labs we got it sorted.

<!-- more -->

# The issue

If you are using Xcode 8 or Xcode 9 (beta 1 as of this writing), when you have a project with mixed Objective-C and Swift, you might encounters problems with autocompletion inside the Swift code.

We've had this intermitently and most recent IDE versions made work very difficult as we would not have any autocompletion for our Swift code, or it would work intermitently.

Our gut feeling was that the bridging header had some classes that were causing SourceKit to fail, but we didn't know how to debug the issue nor provide good feedback for Apple to fix it. Our chance came while visiting the labs at the conference, and a very helpful and patient Apple engineer worked with our project to understand what was wrong.

# Bridging header imports

It turns out that if you import classes from libraries (in our case using CocoaPods) omitting the path may result in Sourcekit unable to index properly.

For example, a class in some library:

`#import "MyClass.h"`

Code will compile fine but your code completion in Swift side might break because the indexer fails when encountering this file. I'm still unsure if it's because of importing the file somewhere else differently or if it's just a bug in the indexer. Anyway, the way to work around it (Apple is supposedly aware of this) is to import with the framework or library style, including the path:

`#import "path-to-library/MyClass.h"`

# How to debug

The engineer kindly made a wrote up for us the steps to be able to debug this in the future. He also shared how to start Xcode in debug mode. I knew that would be possible, but didn't know how to do it before.

To open Xcode with debug menu, open it from command line with the argument:

```bash
/Applications/Xcode.app/Contents/MacOS/Xcode -ShowDVTDebugMenu YES
```

There's many debugging entries enabled! For our exact problem we're interested in Sourcekit, and the menu entry is i "Xcode -> Internal -> SourceKit -> Service Log".

To investigate the code completion problem:

- Invoke code-completion, then close the log window
- Open /tmp/sourcekit-service-log.txt
- Search for "key.request: source.request.codecomplete"
- Get the compiler arguments from "key.compilerargs:" and create a swift invocation with them:
    - Add "DEVELOPER_DIR=/Applications/Xcode.app xcrun swift -frontend " in front of the arguments
    - Add "-primary-file" for the file mentioned in "key.sourcefile:"
    - Remove "-Xfrontend" flags
    - Remove "-j<N>" flags

Juggling the arguments is easier to handle if you copy all the argument list to a file and make it executable by bash, as you would run the script in the file instead of pasting arguments into command line.

Here's example of how the command would look like, for a file "/project/path/myfile.swift". I'm omitting whole argument list, just ilustrating how it would look like.

```bash
DEVELOPER_DIR=/Applications/Xcode.app xcrun swift -frontend -primary-file "/project/path/myfile.swift" "-module-name" "Peak" "-Onone" "-DDEBUG" "-sdk" "/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS10.3.sdk" "-target" "arm64-apple-ios8.0" <many more>
```

After running the command, you'll see errors like this one:
/project/path/My-Bridging-Header.h:10:9: note: in file included from /project/path/My-Bridging-Header.h:10:9: #import "MyClass.h"

You now know the headers that need fixing and now can change in the briding header.

# Conclusion

We got a functional IDE once more! Apple is very helpful at the WWDC and it is very good for these and many other questions to be answered or investigated. If you attend the conference anytime, be sure to bring your questions, code, and issues to talk directly with Apple engineers.

I hope this helps anybody that is asking 'why my autocompletion in Swift broke in Xcode'. This might be cause of the issue. The debug menu can be helpful to poke into the internals of the IDE, or just to help Apple fix bugs by providing more detailed logs.

Happy bug hunting!

[peak]: http://peak.net
