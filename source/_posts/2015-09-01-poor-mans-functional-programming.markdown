---
layout: post
title: "Poor man's functional programming"
date: 2015-09-01 00:20:10 +0100
comments: true
categories: ["swift", "functional programming", "meta"]
---

It's a very exciting time to be an iOS or Mac developer. The platform moves very fast and we got a new toy (language) to play with: Swift. It's the perfect time to reevaluate, learn and evolve as a programmer, because you will be forced to adopt this new language (yes, I think Swift is the future, 100%).

I want to relate this to the fact that most iOS engineers, or mobile application developers, are traditionally familiar with Object Oriented Programming paradigm. But Swift offers more than different syntax and OOP.

I’m referring to *some* features inspired by functional languages. I’ve you’ve ever had an interest, but never the chance or motivation to go forth and look into this paradigm, now it's the perfect time to do it.

<!-- more -->

# Functional programming is...

I don’t want to start defining what functional programming is, there’s [excellent](http://harlankellaway.com/blog/2015/08/10/swift-functional-programming-intro/) resources out there, [check them out](https://www.objc.io/books/).

I want to stress out how a beginner, somebody who starts learning Functional Programming (FP), might think think:

- Too abstract
- Not applicable to my problems
- For academics
- What does even ‘pure’ mean?
- I **need** to mutate stuff!
- Meh, I’ll go back to my familiar way of doing things

I’m not claiming that the previous statements are true, I’m just rephrasing and accumulating thoughts **I** have previously had. Most possibly other people had similar thoughts.

There’s a higher barrier of entry to learn how to think and code programs which are more ‘functional’. The concepts are abstract,  and the benefits are often described as *it’s more functional* without emphasis of what you are actually gaining. Also there’s the problem that often times all is explained by simple examples. But every programmer knows there’s a huge difference between toy programs and real big projects.

# Flame wars

On the other hand side of the equation you’ll find engineers and computer scientists that believe that the *only and true* way of doing things is of course thinking functionally, and we’ve all been doing it wrong during the last decades. The math is the only source of truth and validity, hence FP is the only way.

They’ve got their very valid reasons to think this way, but my opinion is that many people familiar with FP start to use higher-level vocabulary, and describe conventions and problems in a way that might seem foreign and strange for somebody who is not used to those terms. Rob Napier has [an excellent article describing this](http://robnapier.net/haskell-overlords).

Computer science, and programming in particular, is a curious field where people take sides around things that may seem totally absurd for an outsider: Vim vs Emacs, Windows vs Mac, Linux vs everything else, Ruby vs Python, OOP vs FP.

Do we really need to fight over who is right, instead of using the tools that are most appropriate to do our job? I’m totally on the side of moving on and being pragmatic about learning, and eventually as growing as a software engineer. By looking at different ways of solving a problem you can only get better at what you do, not worse, because you will have the power to make an informed choice.

I’d love to see the people who are experts or versed in FP to reach out and share knowledge in a way that can be understood by beginners. If we really want people to be aware of the benefits of using a different paradigm, we should make it approachable to start with.

# Poor man’s functional programming

I would like to think that for most engineers, the choice of paradigm on our day to day work is not a clear one. Thus we will have direct benefit from a language that let’s you choose. We will choose to write code in an OOP way, and then use FP where it benefits the most as well. And it seems I’m [not](https://realm.io/news/altconf-saul-mora-object-orientated-functional-programming/) the [first](http://www.johndcook.com/blog/2009/03/23/functional-in-the-small-oo-in-the-large/) to think about this.

Coming back to Swift, this is precisely what you can do with it. You can choose programming styles depending on what your problem is, the framework you work with, and even the team you work on. 

This flexibility does comes with a price though: The functional features of the language are not as rich as other, *purer* languages. So in a way, that’s the whole point of my essay: With Swift, and given the normal users of the language (myself included), we will be able to do **Poor man’s functional programming**. A style that will not be purely functional programming, but won’t be only imperative OOP programming.

We’ll make mistakes as we learn, but hey that’s part of our job. Do you like code you wrote 6 months ago? I know I don’t.

# Moving on

I like to be [pragmatic](https://pragprog.com/book/tpp/the-pragmatic-programmer) rather than zealous. And I think Swift brings an excellent opportunity to learn more about other ways of thinking about building software, different abstractions, and how to fit those into our day to day work as a mobile developers.

Swift, as [other modern languages](http://www.scala-lang.org/), offers a unique opportunity to learn and grow. It’s a very exciting field we work on.

*Aside: Sources to check the ‘tension’ between OOP and FP:*

- [Functional on the small, OO in the large](http://www.johndcook.com/blog/2009/03/23/functional-in-the-small-oo-in-the-large/)
- [I have no idea what I’m doing](https://realm.io/news/altconf-graham-lee-i-have-no-idea-what-i-m-doing/)

- [OOP is an expensive disaster](http://www.smashcompany.com/technology/object-oriented-programming-is-an-expensive-disaster-which-must-end)

- [Why hasn’t functional programming taken over yet](http://stackoverflow.com/questions/2835801/why-hasnt-functional-programming-taken-over-yet)

*And the list goes on and on...*




