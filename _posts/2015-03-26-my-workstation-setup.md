---
layout: post
title:  "My Workstation Setup"
date:   2015-03-26 12:00:00
meta: "A breakdown of my setup, for future reference (and curious bystanders)."
categories: blog
tags: misc
featured: true
image: macbook.jpg
---
A good workstation is essential.  At the moment, I'm pleased with mine.  Nothing too fancy and out of the ordinary, but effective for everything I need.  It helps starting from a nice MacBook Pro with a Retnia Display.  This has me covered when I develop Windows Apps/Services, iOS Apps, and various things running on flavors of Linux.  Being able to boot into any drive/OS directly or virtually is handy.  Also, I find the [gesture](https://support.apple.com/en-us/HT4721) support extremely productive (ex: switching applications, desktops, etc...) and a good compliment to shortcuts.

I have my personal MacBook Pro, plus my work one (issued from the company). Mostly for my future reference and updates (but also for anyone curious), here is a high level rundown of my typical setup.

###Preparation
From a new Mac with the latest OS X, check for any updates (using the App Store).  If it's my personal Mac, associate iCloud and iTunes to my account (to get iCloud syncing, Handoff, text/call support, etc...). Then copy over any items needed (at a minimum, my e-books for reference).  Attach to any cloud drives for the necessary items to sync/copy (ex: Google Drive, Dropbox, OneDrive, etc...).

###Install Apps from App Store
- Pages, Numbers, Keynote
- Pixelmator
- Xcode
- Caffeine
- Remote Desktop
- Slack

###Install Apps Not From the App Store
- Sublime Text
- Google Chrome
- VMware Fusion
- Office for OS X
- Skype
- [SuperDuper](http://www.shirt-pocket.com/SuperDuper/)

###Install / Configuration Commands
Install and configure [Homebrew](http://brew.sh/), the essential package manager for OS X.

    $ ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)"

Once installed, run through the initial checks and get the essential things.
 
    $ brew doctor
    $ brew install git
    $ brew install python
    $ brew install node

Since older/existing versions are already installed for OS X, and to ensure other items installed using `brew` have priority, update the path to include the brew location.

    $ cd ~
    $ nano .bash_profile
    
    # export an appended PATH by adding the following
    export PATH=/usr/local/bin:$PATH
    
    # to exit and save
    ^O enter ^X

Add virtual environment support for Python.

    $ pip install virtualenv

Install [CocoaPods](https://cocoapods.org/), the package/dependency manager for Swift and Objective-C projects.

    $ sudo gem install cocoapods

Setup the e-mail client.  For personal GMail, enable IMAP and turn off IMAP updates for unimportant things (ex: "tags", "chat", etc...).  This is done in GMail under Settings - Forwarding and POP/IMAP.

###Windows 
Next step, getting Windows up to speed. While still in OS X, run the Boot Camp Assistant from Applications - Utilities.  Ensure a thumb drive is handy for the drivers, along with a Windows ISO or install disk.  Walk through the wizard and Windows installation, with a cup of coffee (or two).  Once installed and running natively, run through the ususal Windows Updates to get current.  Also, when running on the Boot Camp partition natively, activate Windows with the valid key entered.  Windows will be activated twice, so it can be run directly from the Boot Camp drive along with running virtually within OS X (see below).

###Install Windows Applications
- Office 2013 (or the latest version).
- Visual Studio 2013 (or the latest version).
- [ReSharper](http://www.jetbrains.com/resharper/), along with [dotPeek](http://www.jetbrains.com/decompiler/).
- [GitHub for Windows](https://windows.github.com/)
- Skype, [Slack](https://slack.com/getting-started)
- [Fiddler](http://www.telerik.com/fiddler)
- [Paint.NET](http://www.getpaint.net/index.html)

###Additional Configuration
When booted back into OS X, run VMware Fusion and the boot camp partition with Windows.  From the VMware Fusion - Virtual Machine menu, install the VMware Tools.  This allows the Windows installation to work nicely when run virtually (as a virtual machine from OS X using VMware Fusion).  With the tools installed, Windows can be activated a second time while running virtually within VMware Fusion. 

If Windows switches to an un-activated state when run virtually, and Windows won't activate with the same key (saying the key is in use on another machine), add `SMBIOS.reflectHost = "TRUE"` to the `Boot Camp.vmx` file in the virtual machines folder (typically located for OS X at `Users/[username]/Library/Application Support/VMware Fusion/Virtual Machines/`).  Also, it may be necessary to set the registry key `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\vmrawdsk\Parameters` to `C:\windows` or `c:\windows\`.

###Conclusion
High level, but helpful (at least to my future self).  A few hints may be helpful to you as well.
