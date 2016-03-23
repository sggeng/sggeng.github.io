---
layout: post
title:  "My Brew Cheat Sheet"
date:   2015-04-03 19:30:00
meta: "Using Brew on my Mac is so handy. But a reference is helpful."
categories: blog
tags: misc
---
This is nothing especially new, but `brew` is a breath of fresh air when installing and updating items on my Mac.  Installing and keeping things up to date is a matter of simple commands, much like `apt-get` for certain flavors of Linux.  For future reference, here is a quick run down.

Install `brew` on Mac OS X (note: `ruby` is already shipped with OS X).  Primary reference and documentation is [here](http://brew.sh/).

    $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

After installation, or updates to `brew`, check to ensure all that is well.

    $ brew doctor

In order to update `brew` itself.

    $ brew update

To update packages that are installed.

    $ brew upgrade

To see what's installed

    $ brew list

To get details about a package, which is helpful to get the details that may have blown past during an installation.
 
    $ brew info [package name]

To look for a package.

    $ brew search [package name]

And finally, to remove a package that's installed.

    $ brew uninstall [package name]
 
 Hope this helps :-)
 