---
layout: post
title: "PGnJ 1.1 - Free For Good, QuickJoin, Crasher Fix"
alias: /post/157711510/pgnj-1-1-free-for-good-quickjoin-crasher-fix/index.html
---

After 8 months of being on sale, I would like to announce that [PGnJ 1.1](http://thomasmango.com/projects/pgnj) is now available and that it is completely free (and that it will absolutely stay free).

It comes down to the fact that the vast majority of PGnJ's user base decided to stay on 0.8 (the last free version). There wasn't huge interest in a pay version of PGnJ and because of that, interaction with customers along with feedback totally dropped off the map. But I still had things I wanted to add to PGnJ. I still use PGnJ every day. I wanted people to get those new features. So, the PGnJ-for-pay experiment is officially over.

What's new in 1.1? The [changelog](http://thomasmango.com/files/PGnJ/changes.txt) may look pretty slim but it comes with a couple of very major improvements as well as a fix for a crashing bug that came about in the past couple of weeks.

Changelog:

* PGnJ is now **free**. Enjoy!
* When using the DataBrowser (double click a table from the database tree in the sidebar), you can how double click any cell to edit the contents (PostgreSQL and MySQL only).
* Developed a brand new, one of a kind feature that I call QuickJoin. When you open the DataBrowser, it will automatically add a QuickJoin bar to the top of the window with a list of all foreign keys for the current table. Clicking a foreign key will bring up a drop down list of all fields in the referenced table. Selecting a column (you can select as many as you want, across as many referenced tables as you want) will automatically generate JOINs under the hood and display the selected column right inline next to the fk column. See below for a quick demonstration.
* Fixed a major crashing bug that came about with OS X Java Update 4. Essentially, the application must be run in 32 bit mode.

<iframe src="http://player.vimeo.com/video/28060086?byline=0&amp;portrait=0&amp;color=195baa" width="600" height="473" frameborder="0"></iframe>