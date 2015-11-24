---
layout: post
title: iOS Code Injection
date: 2014-11-23 12:21:36 +0000
comments: true
categories: [tools, productivity]
---
Write code, compile, **wait**, navigate to screen in application. Fix problem, recompile **wait**. Oh it was not fixed, start again... Does it sound familiar? For compiled languages there seems to be no workaround this bottleneck. Even though this is true, there are situations where we can reduce the waiting time, increasing our productivity and happiness. Enter code injection.
<br/>
<!-- more -->
#The problem
When a program is launched by the OS, the code is read from the binary file and loaded in volatile memory and it is then executed by the CPU. When that program is written in a compiled language, the classic approach to update the code is to kill it, recompile it, and load it again from scratch. This guarantees a fresh start and ensures you are running your *'bleeding edge'* version.

The problem with this approach is that recompiling and relaunching the program can take a small, but not insignificant, amount of time. One recompile-relaunch cycle may not be a waste of time in itself, but a developer can go through this cycle hundreds of times in a day, so the time sums up considerably. Adding to this problem, if the program itself is an application, then the developer needs to navigate to the section she is working on and interact with the UI manually to check her updates.

###What about iOS apps?

Thinking in the domain of iOS apps, this fact is even worse. Many iOS apps are not overly complicated, but the process of developing an application
is time consuming and involves hundreds of iterations and small tweaks to a screen. Working in an environment where small iterations evolve a product,
it is not uncommon that a developer needs to change small UI values or needs to do some small logic changes to a small part of an application. Being highly graphical applications, it's faster to develop and 'transform' the code if you can see your changes directly updated on the screen after saving.

#Code Injection

Code Injection is a way of inserting code in a running process without killing it and relaunching it. You still need to recompile the changed files, but if you inject the code you can remove all the waiting time to relaunch, and the time to setup the state of your application, like navigating to a specific screen.

###Available tools
Currently there are two open source tools available:

- [injection for xcode](https://github.com/johnno1962/injectionforxcode)
- [dyci](https://github.com/DyCI/dyci-main)

Both tools take advantage of Objective-C runtime to modify the code while it is running. *Injection for xcode* offers more features, such as some adjustement sliders, and integration with Apportable, or recent (limited) Swift support.

###How does it work?

You can always look at the source code for details, but in a nutshell:

The tool will be watching your source code and then will trigger a recompile when changes are found. The recompile is done through clang directly instead of through XCode. A client side of the tool will be running in the device, waiting to changes to be *'injected'* into the application. Then, leveraging Objective-C runtime, the actual code is modified.

After the code is modified, the tool will offer you as developer ways to *'reload'* after changes; you may want to load a whole view controller back into memory from it's XIB file, or trigger *setNeedsDisplay* in your custom view.

#Using dyci

As this is my tool of choice, I will show you how to use it.

##Set up

1. Clone the repository and run install script
```bash
git clone https://github.com/DyCI/dyci-main
./dyci-main/Install/install.sh
```

The script is doing three things:

- *Proxy* clang to capture the build commands it issues, so it can know how to compile your source code
- Installs plugins for XCode and Appcode in your computer
- Sets up a script and .dyci folder in your $HOME

2. Embed client code into your app

Dyci will run in your application, watching for a .dyci directory in your $HOME, so you need to include it in your code. I prefer to use Cocoapods as it is simpler. Ensure you don't ship this to your users, by linking it to debug configuration only. In your podfile:

```ruby
pod 'dyci', :configuration => ['Debug']
```

##Usage

###Triggering an rebuild
You have two choices to trigger a recompile of your code. The first one is to use the provided plugins for XCode or Appcode.

A second choice is to run the rebuild script yourself once a source file is saved in your repository. I first learned this technique from [Krzystof Zabłocki's](https://github.com/krzysztofzablocki/KZPlayground) approach in KZPlayground, and haven't used the plugins since.

You will need a file watcher tool, for example [kicker](https://github.com/alloy/kicker), to trigger scripts when files are changed. The script you want to run is installed by dyci under *$HOME/.dyci/scripts/dyci-recompile.py*.

To trigger recompiles using kicker, you need to install the gem and to create a custom recipe. I prefer to make the recipe available globally in your system:
```ruby
gem install kicker
cd ~/.kick
touch code_injection.rb
```

The custom recipe in ~/.kick/code_injection.rb:
```ruby
recipe :code_injection do

  process do |potential_files|
    files = potential_files.take_and_map do |file|
        if file =~ %r{^.*\.(swift|m|xib|storyboard|strings|png|jpeg|jpg)$}
          cmd = ['/usr/bin/python']
          cmd << File.expand_path('~/.dyci/scripts/dyci-recompile.py').safe_shell_path
          cmd << File.expand_path(file).safe_shell_path

          command = cmd.join(' ')
          puts "Executing #{command}"
          execute(command)
          puts "DYCI: Recompiled #{file}"
          file
        end
      end
  end


  process do |remaining_files|
    remaining_files.take_and_map do |file|
        puts "DYCI: Ignored #{file}"
      file
    end
  end

  startup do
    log "DYCI: Kicker watching for file changes to recompile!"
  end

end

class String
  def safe_shell_path
    "\"#{self}\""
  end
end
```

Then on your repository root run:
```bash
kicker -r code_injection
```
Trigger a full project recompile and run your application. From now on, when you save a file, it will automatically be recompiled. Save a file and test in your console that the file is recompiled, you should see the logged lines and a success. The script will generate a dynamic library that can then be loaded by the client code in your application.

###Injecting the code

Once dyci has rebuilt your modified file(s), the code running in your application will grab the newly built dynamic library and load it into your application. Then it modifies the necessary code.

Once your application is running, check that upon initialization, the app is watching for changes under your ~/.dyci directory. If this is not the case, then the app will not receive new built libraries and your code will not update.You should see some log lines like:
```
2014-11-26 11:23:49.242 InjectionObjC[63278:435583] DYCI directory path is : /Users/mquinones/.dyci
2014-11-26 11:23:49.249 InjectionObjC[63278:435583] ============================================
2014-11-26 11:23:49.249 InjectionObjC[63278:435583] DYCI : Dynamic Code Injection was started...
2014-11-26 11:23:49.250 InjectionObjC[63278:435583] To disable it, paste next line in your application:didFinishLaunching: method :

[NSClassFromString(@"SFDynamicCodeInjection") performSelector:@selector(disable)];

2014-11-26 11:23:49.250 InjectionObjC[63278:435583]      or
2014-11-26 11:23:49.250 InjectionObjC[63278:435583] Simply remove dyci from dependencies
2014-11-26 11:23:49.250 InjectionObjC[63278:435583] ============================================
```

So, using XCode, when you save a file it gets recompiled and injected, watch for this in console:
```
2014-11-26 11:29:10.402 InjectionObjC[64757:439975] New file injection detected at path : /Users/mquinones/.dyci/dyci3950098.dylib
2014-11-26 11:29:10.402 InjectionObjC[64757:439975]
2014-11-26 11:29:10.402 InjectionObjC[64757:439975]  =================================================
2014-11-26 11:29:10.403 InjectionObjC[64757:439975] Found new DCI ... Loading
2014-11-26 11:29:10.413 InjectionObjC[64757:439975] DYCI was successfully loaded
2014-11-26 11:29:10.413 InjectionObjC[64757:439975] Searching classes to inject
2014-11-26 11:29:10.419 InjectionObjC[64757:439975] Injecting  class : ViewController
2014-11-26 11:29:10.419 InjectionObjC[64757:439975] Injecting meta class : ViewController
2014-11-26 11:29:10.419 InjectionObjC[64757:439975] Class (ViewController) and their subclasses instances would be notified with
2014-11-26 11:29:10.419 InjectionObjC[64757:439975]  - (void)updateOnClassInjection
2014-11-26 11:29:10.426 InjectionObjC[64757:439975] 1 (ViewController) class instanses were notified on Class Injection :
2014-11-26 11:29:10.426 InjectionObjC[64757:439975] 1 instanses were notified on Class Injection by injecting class: (ViewController)
2014-11-26 11:29:10.427 InjectionObjC[64757:439975] Class was successfully injected
2014-11-26 11:29:10.427 InjectionObjC[64757:439975]
```

###Updating after injection

So your code is injected, but nothing happens in the user interface? If you think about it, this is completely normal, as iOS is not aware that something changed, so nothing is updated in your screen, unless you force it to do so. The updates you may want to do are different depending on what you are modifying, so bear in mind that just injecting the code is not enough.

 The library offers some hooks where you can write test-only code to update what you need. Those methods are declared in the category NSObject+DyCInjection. Use them as you want to trigger *layoutSubviews* *reloadData* for example.


###Pixel perfect tweaks

You generally need to ensure that what your designer wanted is shown exacly as she wanted in your application. I Use code injection, paired with a very simple app: [Uberlayer](https://itunes.apple.com/us/app/uberlayer/id510139938?mt=12).

This app lets you put an image over anything in your screen. Overlay it exactly on top of iOS simulator and you can incrementally update the code till it matches perfectly a desired application design.

###Gotchas

After you install a new version of XCode, dyci needs to be reinstalled, because dyci is not proxying clang anymore.

Some code is harder to modify using code injection, for example the code in a singleton initialization.

Dyci only works in simulator, because of the hook in your computer's file system. Injection for xcode does work on devices though.

I didn't mention Swift because it is a less dynamic language than Objective-C and I expect injection to be harder to do. 'injection for xcode' added limited support for it though.

**Do not** link against the code injection library in release, or you risk removing it and shipping it to your users. You really don't want that.

#Conclusion
No more excuse for this:
<br/>
![image](http://imgs.xkcd.com/comics/compiling.png)
<br/>
We have reviewed a very useful tool to avoid long compile-wait-launch-navigate cycles and improve productivity. Code injection is not a silver bullet  of course (is there a silver bullet in programming?), but a very useful tool to use for small changes to code or prototyping. You should really consider using it more often as it can be a huge time saver.

###Good for:

- Tweaking UI values
- Pixel-perfect tweaks: Paired with my favourite pixel-perfecting app: [Uberlayer](https://itunes.apple.com/us/app/uberlayer/id510139938?mt=12)
- Changing existing logic
- Prototyping
- Changing strings
- Visual debugging

###Not so good for:

- Writing new code and debugging it
- Unit testing
- Changing singletons or long-lived application objects (like services)
- Rewriting lots of code
