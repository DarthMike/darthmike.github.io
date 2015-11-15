---
layout: post
title: "Jumping into existing codebases"
date: 2015-11-15 18:55:18 +0000
comments: true
categories: iOS git tools productivity
---

I’ve recently changed workplace - [we’re hiring!][peak] - and I’ve jumped into an existing codebase. This is a good time to review what I like to do when I start to work on a project in these circumstances.

<!-- more -->

When you don't start a greenfield project, which is most of the time for many developers, you need to rapidly and effectively get to grasp with all the code, so you can become productive and acknowledgeable with any changes that need to be done. There's some things I always do in these cases.

# Examining the repository

## Git stats
I always like to start with the repository itself. Looking at the history, commits and overall activity. Many times you will find that developers are not used to providing consistent and meaningful commit messages (how many times you’ve seem **WIP feature X** commit message?). So looking at the history is not informative. So I prefer to look at global statistics instead.

Assuming you will work with a git repository, the first tool I like to use is [git stats][git_stats]. As easy as:

```bash
gem install git_stats
cd <repo>
git_stats generate
```

Running this tool will result in a nice html document giving insights about authors, commits, files and lines of code. It’s mostly informative but it’s good to get an overview of the key collaborators of a project, who created, and most importantly, who deleted the most lines of code!

### Gource

A very interesting way to see the commit history in a graphical way is [gource][gource]. I always like to run it for 5 minutes just to get a feel of the rush of the project, and basically to see
in a glance what are the core parts of the codebase. It also is very funny to look at, and you can get a feel what is the core part of the repository, what parts changed the most, and the number of developers working at the same time. It's available via [homebrew][homebrew] as well so very easy to install and run:

```bash
brew install gource
cd <repo>
gource
```

Check the available options, like `seconds-per-day`, to customize how you see the animation. I like to run it with 2 seconds per day, to make the visualization move faster (and be funnier to watch!).

## Commit messages

I like to just do a `git log` in terminal and understand if the team has any kind of agreement on what and how commit messages are structured. Having good commit messages beats any amount of commented code kept around and is a very integral part of the project.

There has been plenty of discussion on the subject, I’m just going to link here 2 of my favorite articles: [this][commit_message] and [this one][commit_message2]. I don’t adhere to all the points presented but agree to many of them. You’ll get an overview of how a project has evolved by looking at some logs, specially from the beginning. Just do:

```bash
git log --reverse
```

Having good commit messages helps a lot when dealing with bug fixes, using [git bisect][git_bisect]. When you're the new one in a project you don't have the domain and code knowledge to understand what changed or debug, you can save hours of pain for  bug fixes by using it, and when you nail down the commit that broke something, you can understand the motivation behind it much better.

## Structure

Just a quick glance of the repository structure can give you a feel of the (lack of) organization and practices of the team. Step number 2 after checking commit logs is browsing the project structure. I'm not going to judge or give opinion on what project structure should be the best, as it very much depends on the project, but I generally will try to identify these kind of groups:

- Code
- Assets
- Submodules or dependencies
- Utility scripts

If the repository does not even contain separate folders for this then you know what is your first task!

## Dependency management

Once I've checked the commit logs, and project structure, before I move on to the code, my last step is to understand the strategy for dependency management. As I work on iOS projects, let's see the options for these:

- Pre-built libraries
- Copied code (ideally in a sub folder)
- Submodule
- Cocoapods
- Carthage

Every project will have a combination of strategies, as many times libraries come from various sources. But if there's a chance, I like to suggest and improve this if possible, and unify how libraries are managed in the project. My opinion is that it's easier to manage dependencies if they are contained and imported the same way.

Many projects will use Cocoapods as the dependency management, even for internal libraries. If that is the case, my last stop is the Podfile. If the team hasn't yet, I just suggest to fix the version for all but internal libraries. This is a practice I consider a must if working with 3rd party libraries, as many unexpected bugs can occur if you import libraries with relaxed version requirements, or just the latest one.

# Code

It is said that a programmer spends most of time reading code rather than writing it, and it will be specially true when you jump into an existing codebase.

Reading (and understanding) the code of an existing project is very daunting at first. How much will take you to understand the fundamental bits depends on many factors, including domain knowledge, code style, code organization and background of the key project developers.

Before diving into specifics of the code, I like to get an overview:

- Lines of code tool
- Dependency visualiser

## Sloccount

[This tool][sloccount] will give you a summary of lines of code in the repository. You will get a feel of overall complexity of code, and if you have source code for dependencies, you can also check which are the bigger dependencies in terms of code size.

Once you take part in more than one project you'll be able to get a feel when a project is *big* or *small*, which is very subjective, but useful to know, when you approach new code.

This tool is again available via [homebrew][homebrew], so it's very easy to run in any project:
```bash
brew install sloccount
sloccount <source_directory>
```

## Dependency visualiser

The biggest problem of reading new code is that everything is kind of new: Domain, code style, requirements, patterns. A very good way to get a grasp of the code structure is visualise the relationships. I know of two tools that do that for Objective-C: [objc-dependency-visualizer][dependency-visualizer] and [objc_dep][objc_dep].

I quite like [objc-dependency-visualizer][dependency-visualizer] because it's easier to use to get a broad picture of relative code size between classes and relationships. Just be warned that for medium to big projects the visualisations are just messy. That's when you can use [obj_dep][objc_dep] to get graphs for specific files or modules.

## iOS projects

Before I go into reading specific code, I do these checks first:

- Run static analyser
- Compile project and see warnings
- Basic objc good practices (swift is very new for practices yet)


### Static analyser

I will run the static analyser, which is the part of the toolchain that will generally be overlooked by most teams. Developers don't trust analyser because it can give false positives, but in my opinion clean code should have 0 analyser warnings.

### Compile warnings

With Objective-C and Swift, we get a program to check our code before it's even run. Many see the compiler as a nuisance, specially those used to interpreted languages. I will run a compilation first to see if the project has warnings, treats warnings as errors, and how many outstanding warnings the team is used to have. I consider Jon Reid's [xcconfig][xcconfig] a very good base line for any project.

Ideally I would like to work on code with most warnings turned on, and warnings treated as error, but this is not the case for many projects out there. The general problem with Xcode is that it creates projects with very lax default warning settings. So many developers don't bother changing them, thus having an unsafe environment for the project from the beginning.

If you start a new project, I'd recommend using a tool to configure properly the project, like [liftoff][liftoff] or [crafter][crafter]. For project warnings, I personally prefer to have a .xcconfig file because it's more readable, maintainable, and allows for comments of disabled warnings. See [xcconfig][xcconfig] or [xcconfigs][xcconfigs] for examples.

### Basic good practices

My last step when surveying new code, is checking the safety of the code. I'll check for basic safe usage of Objective-C which is a very punishing language with lots of sharp edges that can cut the unaware developer ;)

- Safe use of BOOL: See http://nshipster.com/bool/
- Correct use of copy: For classes having mutable subclasses - NSArray, NSDictionary, NSString, NSData...
- Safe use of blocks: Checking for nil block parameters
- Memory leaks and blocks: Has the code a simple definition of [@weakify and @strongify][weakify], or dealing with retain cycles with blocks in a similar way?
- Modern code: Uses [nullability][nullability] and [generics][generics]?

# Start coding!

I hope you get some useful tips from my review of tools and checks I do when jumping into new projects. I you've got any more, [tell me!][twitter].

[peak]: http://peak.net
[git_stats]: https://github.com/tomgi/git_stats
[commit_message]: http://chris.beams.io/posts/git-commit/
[commit_message2]: https://robots.thoughtbot.com/5-useful-tips-for-a-better-commit-message
[git_bisect]: https://git-scm.com/docs/git-bisect
[gource]: http://gource.io
[homebrew]: http://brew.sh
[sloccount]: http://www.dwheeler.com/sloccount/
[dependency-visualizer]: https://github.com/PaulTaykalo/objc-dependency-visualizer
[objc_dep]: https://github.com/nst/objc_dep
[liftoff]: https://github.com/thoughtbot/liftoff
[crafter]: https://github.com/krzysztofzablocki/crafter
[xcconfig]: https://github.com/jonreid/XcodeWarnings
[xcconfigs]: https://github.com/jspahrsummers/xcconfigs
[twitter]: https://twitter.com/miguelquinon
[weakify]: https://github.com/jspahrsummers/libextobjc/blob/master/extobjc/EXTScope.h
[nullability]: {{site.url}}/blog/2015/04/17/adopting-nullability-annotations/
[generics]: {{site.url}}/blog/2015/06/09/adopting-objectivec-generics/
