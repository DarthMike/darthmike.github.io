---
layout: post
title: "Knowing when to delete code"
date: 2016-08-16 21:04:04 +0100
comments: true
categories: [tips, meta]
---

Much of the literature and writing related to sofware development focuses on the creation, new ideas, fresh ideaas and things to improve in your next project. But when is it a good moment to delete the old stuff in an long term project? Here's my short rant about this; I recently deleted a chunk of code in my current project and just felt I wanted to write my thoughts.

<!-- more -->

# Short-lived code

Working on application development field, it is very normal that some code is very short lived. If you're looking to build systems and generic components then you're generally out of luck. Most Application code is generally short lived. In my opinion we just need to know how to deal with it.

Products evolve, ideas and markets change, and the code implementing those concepts has to change to reflect the direction of an application. If your code doesn't closely relate or express the domain of your application, you're bound to have design problems, miscomunication and likely many bugs. Application code is very likely to become legacy in a very short time. That time might be even less than 1 or 2 years.

# Legacy code

There isn't consensus on what makes code reach the dreaded *legacy* status. The definition has changed since it's first introduction, and nowadays we generally consider legacy the code that we didn't write. See the [wikipedia entry][wikipedia]:

> ...source code inherited from someone else and source code inherited from an older version of the software

Or another definition:

> Michael Feathers introduced a definition of legacy code as code without tests, which reflects the perspective of legacy code being difficult to work with in part due to a lack of automated regression tests

Many programmers generally call legacy code anything that they didn't write. While this is a very harsh treament of somebody's effort and time, it will mean that **your** code will become legacy in very short time as well.

# Refactoring vs Rewriting

Legacy code is often mentioned when refactoring. The effort of writing some piece of functionality again versus the cost of refactoring code is something we all need to consider at some point. I think that you should always evaluate this in the light of your project and always ponder the real end-user value of your rewrite or refactor. Sometimes if something works [it's better not to change it][wat].

My view is that we should always try to do small refactorings, even if that implies rewriting small parts of functionality. Big rewrites never pay off in my experience, but hey I might be wrong. Read Martin Fowler's [excellent entry][refactoring] on refactoring.

# Clean after yourself

I'm more concerned about dead code than necessary refactoring efforts. We rarely spend the time to analyze our projects and search for dead code. Things we left out after refactoring or an old clean up. The engineering team is responsible to clean the lower decks of the ship, so to speak, and it's our duty to ensure that the environment we work in is clean and up to date. Nobody else will do it otherwise.

I think developers need to emotionally detach themselves from code they wrote, and be ready to delete things that don't work or are no longer relevant. ABTests, temporary user migrations, temporary bridging code between legacy systems and new systems. I think we shouldn't save idle and not used code 'just in case we need it', as it adds to the cruft of the project. You'll forget after a while and nobody will know what to do with it. Just delete it.

To conclude, if you introduce any kind of prize contest in your engineering team, then please, give a prize to the engineer that deleted the most lines of code, not the one that wrote the most. You can be sure that that engineer didn't introduce any bugs nor regressions.

Ripley out.

[wikipedia]: https://en.wikipedia.org/wiki/Legacy_code
[wat]: http://www.cnbc.com/2016/05/25/us-military-uses-8-inch-floppy-disks-to-coordinate-nuclear-force-operations.html
[refactoring]: http://martinfowler.com/articles/workflowsOfRefactoring/
