---
layout: post
title: "Use ctags in osx to index system headers"
category: tech
keyword: ctags, osx, vim, system headers
description: "Recently I've changed my laptop to a new rMBP. Although almost all the files, settings and applications are transfered to new laptop with the help of magical Time Machine, some command-line tools are still missing. So I reconfigured ctags in my laptop but encountered some tricky problems. Now I'm gonna write down and share with you."
---

When using IDE like Xcode to write code, we may be impressed by the convenience brought by "Jump to definition" feature. However, when we write code in command-line environment like vim, we cannot natively enjoy the convenience anymore. But, ctags brings the great feature to command-line environment.

## Install ctags in OS X Mavericks

### ctags in command-line tools from Xcode?

When you install the command-line tools from Xcode, you have a ctags installed and you can use it directly. However, it does not support the -R option (recursive), which is a valid option in linux versions. And I find no way to generate tag files recursively in this version. So we have to abandon it and install it manually.

### Install through Homebrew?

There are many tutorials saying that we can install through Homebrew. Indeed, Homebrew can install ctags in your os with the command:

    brew install ctags

But, it installs to the subdirectory under Cellar(/usr/local/Cellar/ctags/5.8). Even if you add the path to $PATH, the Xcode version is still the default one. You can check it with the command:

    ➜  ~  type ctags
    ctags is /usr/local/bin/ctags

We can add an alias in .bashrc or .zshrc file to redirect the command ctags to Homebrew version. But for an elegant programmer, it's not elegant. So we can just remove the Xcode version and install from source code.

### Install from source code

First, download the source code from [*SourceForge*](http://sourceforge.net/projects/ctags/files/ctags/5.8/ctags-5.8.tar.gz/download). Unzip it and cd into the directory.

    ./configure
    make
    sudo make install

OK, the linux version is installed and you can simply use it with command ctags.

## Generate tags files in system headers directories

Most system headers are located in /usr/include/tags and /usr/local/include/tags. So we change directory into them and type:

    ctags -R .

Now we have tags files in each of the directory.

## Put tags files on vim's radar

You are now one step away from indexing system functions in vim. Edit your personal .vimrc file and add two lines: 

    set tags+=/usr/local/include/tags
    set tags+=/usr/include/tags

Save and exit. Yoohoo~, everything is done!

You can open an arbitary c file and navigate to a system function like printf. Hit ctrl+[ and you can now jump to the definition of printf!


