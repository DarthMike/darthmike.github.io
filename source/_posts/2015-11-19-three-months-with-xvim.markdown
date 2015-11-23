---
layout: post
title: "Three months with XVim"
date: 2015-11-19 22:19:24 +0000
comments: true
categories: tools iOS productivity
---

'Know your IDE' is the mantra of the productive programmer. And I've chosen to go and try to use same keybindings everywhere, by investing time and learning Vim. And using [xvim][xvim] in Xcode. In this article I'm going to try to explain why I did this, and I'll share my experience of the process.

<!-- more -->

Knowing your IDE, and being productive in it is one of the best investments you can do as a programmer. You need to write code, and sometimes we do repetitive tasks if we are involved in bigger projects: Navigation and search, refactoring, some formatting and changing existing code. 

# Vi(m)

vi is a command-line text editor. [Vim][vim] is an extended  of the same editor.

The main difference from 'more modern' editors, is that it has two modes, and it's designed for proficiency in a kind of command language where the user specifies where he wants to navigate and what he wants to achieve.

Many people still use it nowadays, which is surprising given that it looks rather old-fashioned, and the proliferation of different text editing modes has been massive.

If you're interested but not familiar with the editor, you'll find that learning how to use it will be a significant time commitment. There's countless of sources of developers explaining why or why not they'd use such editor; you'll find them easily. The sources that inspired me and I go back to from time to time are:

- [Why, by Jon Beltran de Heredia][tip1]: Very old source but explanatory for somebody not familiar with the editor.
- [Daniel Miessler's intro][tip2]: Very well written examples and thinking mode that goes with the editor.

# Investments

Getting familiar and productive with your IDE is a time investment. It can take you from weeks to years, and it's very recommended you do so, because it's rather useful to produce code with less time to have time for other interesting tasks (or coffee!).

About 3 years ago, I moved all my workflows to command-line. So any use of git, external text file editing, or remote server management, I would do through command line. For the command-line interface I chose vim as my *go-to* editor. But I never learnt more than the modes, and simple editing in one page of text.

A year ago I was already familiar with Xcode, it's keybindings and was moderately productive with it. But I felt I had to improve. The main thing I noticed was I would switch to the mouse many times to navigate a file. Being also a user of [AppCode][appcode], I thought my vanilla Xcode experience was not very productive.

And so I concluded why not try and invest more time with vim, given I would not only use it for iOS development? I installed [xvim][xvim] plugin for Xcode 3 months ago, and I don't want to go back to vanilla Xcode.

# Pros
Control at your fingers
- Hands not moving home rows matters
- Go to CL, fire VIM, do same thing
- One muscle memory
- Use graphical VIM cheatsheet
- Atom (other editors) has vintage mode
- (mostly) unified set of keybindings

# Cons
- Learning curve
- Integration with IDE / keybindings clash
- Not complete vim

# Learning process

# The next step




# TODO: --- REORGANIZE TO ARTICLE ITSELF
# Tips and intro:
Tips and intro:
- [Configuration1][tip3]
- [configuration 2][tip4]
- [cheat sheet][cheat-sheet]

# Notes

- 
- Muscle memory
- CL for everything but code editing
- Ssh into servers
- Midpoint: IDE with vim keybindings (best of both??)
- XCActionbar: https://github.com/pdcgomes/XCActionBar

[vim]: https://en.wikipedia.org/wiki/Vim_(text_editor)
[xvim]: https://github.com/XVimProject/XVim
[tip1]: http://www.viemu.com/a-why-vi-vim.html
[tip2]: https://danielmiessler.com/study/vim/
[tip3]: http://nvie.com/posts/how-i-boosted-my-vim/ 
[tip4]: http://mislav.net/2011/12/vim-revisited/
[cheat-sheet]: http://www.viemu.com/a_vi_vim_graphical_cheat_sheet_tutorial.html
[atom]: http://atom.io
[appcode]: https://www.jetbrains.com/objc/