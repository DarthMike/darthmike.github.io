---
layout: post
title: "Poor man's functional programming: FP in the small"
date: 2015-09-07 22:00:59 +0100
comments: true
categories: ["swift", "functional programming"]
---

In a [previous post][part1], I wrote about the choice of paradigms that Swift presents for it's target audience. I also gave my opinion on how people with different backgrounds might see and approach the Functional Programming paradigm.

In this post I want to share how I started to take advantage of FP with Swift for the practical domain of iOS developers: Building apps.

<!-- more -->

# Silver bullets

In software development there are no silver bullets to kill your problems and move on. We always need to find tradeoffs and evaluate our options to decide what would be the best choice. We often need to consider not only the technical aspects but cultural or team aspects too.

iOS applications involve a great deal of **UI** programming. The iOS frameworks are object-oriented, and Swift is not a pure FP language. Throwing away all the code from Apple's frameworks just to make our code more functional is not a realistic approach.

Thus we need to balance where we want to use from different programming paradigms. For a beginner, or a team with not too much experience with FP, my choice would be doing [Functional Programming in the small, Object Oriented in the large][small_large]. We'll focus on how to leverage Swift to write more 'functional' code in the small.

# FP in the small using Swift

## Basics

Let's review the features of Swift and the standard library that help write more 'functional' without having to restructure or change how we build a project.

### Better functions

One of the first terms you will hear when diving into FP is that functions are **first-class** citizens, or the language supports **higher-order** functions. 

What this means is that functions are treated like any other type. Think as functions being treated like objects; They can accept other functions as input, return other functions, and be stored and passed around.

Under this definition Objective-C *kind of* supports this, but it's lacking in several areas. Firstly, there's a difference between blocks and selectors, the way to use them is different and the syntax is more complex. Furthermore in Swift a closure is a function without name, whereas in Objective-C closures can only be expressed with blocks.

So a simple example is:
```Swift
**TODO**: Example moving around closures/functions
```

### They're everywhere!

Functions are everywhere, even disguised sometimes:

- Methods in are functions that take an implicit 'self' first argument
- Computed properties are closures
- Lazy variables are defined by a closure

Given that functions are everywhere my first recommendation is that you should learn to read and understand the signature. This is specially important when generics are involved, which is a very fundamental part of the language. A function signature conveys two crucial bits of information:

- The **shape** it has, so it can be used somewhere else where another function expects same signature
- **What** the function does. In some cases, specially in the standard library, reading the name and understanding the signature of a function, you can infer what it does. I don't think it replaces documentation but it's a big helper when reading code.

A last good property that I think functions and closures have in Swift, is that the syntax is very concise and easy to understand. The language also has special handling for trailing closures which is very handy to keep the code readable. Also implementing DSLs using closures is very easy. This might seem a picky point, but in my opinion readability of a programming language is very important.

### Elementary functions

Given that FP is about functions, many programming languages offer similar 'basic' basic ones which are the building blocks of more complex logic. Understanding and knowing how to use these can make you start applying a more functional style across all your code easily.

Learn how these functions work, and how you can apply them to several contexts:

- Map
- Filter
- Reduce
- flatMap

There's a great [post][fp_intro_swift] by [Harken Hallway][harken_twitter] that gives practical introduction to some of them.

Ways I like to use them:

```Swift
**TODO**: Map instead of loop
```

```Swift
**TODO**: Reduce to find visible in hierarchy
```

```Swift
**TODO**: Filter in viewModels
```

```Swift
**TODO**: Flatmap with optionals
```

### Operators

Spend some time reading about FP and you will eventually bump into some crazy-looking operators. Even though I don't think they increase readability of code, it's useful to understand how they work.

For starters, remember that an operator in Swift is just a function. It can take two forms, depending on the operands it works with. When you see an operator in my opinion is actually better to think about the function and look at it's signature, rather than implementation, to understand what the operator does.

The reason custom operators are u
See ThoughtBot/Runes
See ThoughtBot/Argo

- bind/flatMap
- apply
- Swift 2 and protocol + extensions

## Conclusion

Changing programming habits is hard, but adopting a new way of writing code can be very rewarding if you understand the benefits that it provides. 

I think smaller changes to ways we code are achievable by every team and it's just a matter of communicating and discussing the benefits through code reviews. Ultimately the goal is to make code simpler and clearer.

Given Swift is a multi-paradigm language it's up to the programmer to choose how to write code, so it's important to  explore various ways until we find what we could call 'idiomatic' Swift.

We are all learning so don't take any of my work for granted, and if you have any comments I'd love to [discuss them][twitter].

[part1]: {{site.url}}/blog/2015/08/31/poor-mans-functional-programming/
[small_large]: http://www.johndcook.com/blog/2009/03/23/functional-in-the-small-oo-in-the-large/
[fp_intro_swift]: http://harlankellaway.com/blog/2015/08/10/swift-functional-programming-intro/
[harken_twitter]: TODO
[twitter]: https://twitter.com/miguelquinon