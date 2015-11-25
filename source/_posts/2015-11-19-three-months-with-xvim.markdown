---
layout: post
title: "Three months with XVim"
date: 2015-11-19 22:19:24 +0000
comments: true
categories: tools iOS productivity
---

'Know your key shortcuts' is the mantra of the productive programmer. I've chosen to try using the same keybindings everywhere, investing time and learning Vim. And using [xvim][xvim] in Xcode. In this article I'm going to try to explain why I did this, and I'll share my experience of the process.

<!-- more -->

Knowing your IDE and being productive in it, is one of the best investments you can do as a programmer. We need to write code, and sometimes we do repetitive tasks if we are involved in bigger projects: Navigation and search, refactoring, some formatting and changing existing code. 

# Vi(m)

vi is a command-line text editor. [Vim][vim] is an extended version of the same editor.

The main difference from 'more modern' editors, is that it has two modes, and it's designed for proficiency in a kind of command language where the user specifies where he wants to navigate and what he wants to achieve. It was also designed to be completely controlled with the keyboard.

Many people still use it nowadays, which is surprising given that it looks rather old-fashioned, and that the proliferation of different text editing modes has been massive.

If you're interested but not familiar with the editor, you'll find that learning how to use it will be a significant time commitment. There's countless of sources of developers explaining why or why not they'd use such editor; you'll find them easily. The sources that inspired me and I go back to from time to time are:

- [Why, by Jon Beltran de Heredia][tip1]: Very old source but explanatory for somebody not familiar with the editor.
- [Daniel Miessler's intro][tip2]: Very well written examples and thinking mode that goes with the editor.

# Investments

Getting familiar and productive with your IDE is a time investment. It can take you from weeks to years, and it's very recommended you do so, because it's rather useful to produce code with less time to have time for other interesting tasks (or coffee!). If you become fast enough, your thoughts will *convert* to code almost instantly, delaying the time between thinking what to do and actually doing. This is specially important when dealing with many files or a moderate amount of complexity.

About 3 years ago, I moved all my workflows to command-line. So any use of git, external text file editing, or remote server management, I would do through command line. For the command-line interface I chose vim as my  editor. But I never learnt more than the modes and clumsy editing in one page of text. I would always rely on external editors to do the bulk of my work.

A year ago I was already familiar with Xcode and it's keybindings. I was moderately productive with it, but I felt I had to improve. The main thing I noticed was my constant switching to the mouse to navigate within a file. Being also a user of [AppCode][appcode], I thought my vanilla Xcode experience was not very productive.

And so I concluded why not try and invest more time with vim, given I would not only use it for iOS development? I installed [xvim][xvim] plugin for Xcode 3 months ago, and I don't want to go back to vanilla Xcode. I'll try and give you an overview.

# Pros

Being proficient with vim has this implications:

- Hands mostly not moving from keyboard for much time; even switching between files can be easily done with keystrokes, so you can move around the project only with keyboard
- It may seem obvious, but having control of the whole project with keyboard empowers you as a programmer, makes you focus more, and lets you reach parts of code faster
- One muscle memory: I'm using vim mode in [other editors][atom]. This means I have to remember less keystrokes. The muscle memory is so strong that I miss it in text editors like gmail. I even started using [vimium][vimium]!
- With one muscle memory, as I use command line a lot, so I will edit very fast and go back to the IDE, with no loss of focus.
- Many other editors have a kind of vim mode available as plugin. So it is a good investment in my opinion.

# Cons

This may be obvious, but the drawbacks of learning vim:

- Learning curve: I'm only scratching the surface and I've constantly forced myself to use Vim keybindings 100% of my programming time. There's so much more to learn, and it has very steep learning curve
- Integration with IDE: It can sometimes be rocky. My experience with the Xcode plugin has been very positive, but I have had crashes here and there. There can also be some key binding conflicts
- Not complete vim: Nothing beats the original editor in terms of functionality and customisation. Using vim this way is a middle ground, but for me personally is a necessary step to continue working with my existing tools, and still learn a new way of moving and editing

# Learning process

As I mentioned, learning vim is a big time investment. It will slow you down at the beginning, but if you are convinced that you want to invest time on it, you'll prevail.

Everybody learns differently, so you must find your way. My suggestions are:

- Print and consult [graphical Vim cheatsheet][cheatsheet]
- Force yourself to only use Vim keybindings for at least a week
- Read some introductory posts or books. The ones I mentioned before helped me a lot

# The next step

There's much you can customise and learn. For now I'll try to be faster and more proficient with navigating and editing Objective-C and Swift code. When I'm really productive, I'll try and learn more advanced features, mainly graphical mode and macros.

I hope you found the article interesting, and that you also are improving your tooling and productivity as much as I am. [Tell me][twitter] what you think!

[vim]: https://en.wikipedia.org/wiki/Vim_(text_editor)
[xvim]: https://github.com/XVimProject/XVim
[tip1]: http://www.viemu.com/a-why-vi-vim.html
[tip2]: https://danielmiessler.com/study/vim/
[tip3]: http://nvie.com/posts/how-i-boosted-my-vim/ 
[tip4]: http://mislav.net/2011/12/vim-revisited/
[cheatsheet]: http://www.viemu.com/a_vi_vim_graphical_cheat_sheet_tutorial.html
[atom]: http://atom.io
[appcode]: https://www.jetbrains.com/objc/
[vimium]: https://vimium.github.io/
[twitter]: https://twitter.com/miguelquinon