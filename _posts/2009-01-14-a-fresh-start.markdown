---
layout: post
title: "A Fresh Start"
alias: /post/70586377/a-fresh-start/index.html
---

I picked up a 320GB 7200RPM hard drive for my MacBook Pro and rather than cloning my current drive, I figured I'd take the opportunity to start fresh.

Starting fresh is kind of scary when you're a software engineer because you have to get your environment setup just the way you like it before you can really get started on work again. While I'm waiting for the last of my files to back up, I thought it'd be nice to detail how I prepare for a fresh operating system install.

I have a Western Digital 1.5TB USB hard drive connected to my machine at all times running Time Machine backups. While I feel this keeps me safe for day to day operations, when I do a fresh install I always do a manual backup of the things I need. You can never have too many backups.

First I copy over my main directories in my home directory. OS X makes this easy. I backup:

* ~/Desktop
* ~/Documents
* ~/Downloads
* ~/Library
* ~/Movies
* ~/Music
* ~/Pictures

I then export important data from applications to make it easy to import in my fresh install. I export from:

* Address Book
* Camino (Bookmarks)

Next, I take various lists of things I have installed and screenshots of my current setup.

* I run `ls -la` in my /Applications directory and copy the output to a text file
* I run `gem list` and copy that to a text file
* I run `which` on various binaries like gem, ruby, rails to ensure my paths are setup properly when I reinstall
* I take a screenshot of my Dock
* I take a screenshot of my Menubar
* I take a screenshot of System Preferences

Stragglers:

* I copy my ~/.ssh directory so that I have my ssh keys
* I copy various config files such as ~/.gitconfig

I then copy my ~/Development directory which contains things like:

* ~/Development/assets
* ~/Development/cocoa
* ~/Development/iphone
* ~/Development/java
* ~/Development/rails

Each of my ~/Development/ directories has directories like ./documentation/ and ./projects/.

Lastly, I make backups of all of my development PostgreSQL and MySQL databases using pgAdmin and MySQL Administrator, respectively.