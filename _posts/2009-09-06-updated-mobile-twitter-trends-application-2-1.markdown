---
layout: post
title: "Updated: Mobile Twitter Trends Application (2.1)"
alias: /post/180883749/updated-mobile-twitter-trends-application-2-1/index.html
---

I wasn't able to add all of the polish and features to my [Trends](http://trends.slicedsoftware.com) application that I really felt was necessary last night, so I've just rolled out a quick update. This is definitely the last update for a while on this app.

<p class="image">
  <img src="http://thomasmango.com/images/2009/06/trends-2-1.jpg"/>
</p>

### Version 2.1

* The front page now lists not only the _currently_ trending topics on Twitter, but it also shows topics that have trended in the past day and the past week.
* Hashtag topics are now working (they were broken in v2).
* The timestamp on the topic page now more accurately states when the topic was _first seen_ trending.
* Added some protection against strange (and actually kind of frequent) errors thrown by the Twitter API when requesting the list of trends.
* If What the Trend doesn't know about a particular topic, rather than throwing an error, you're now redirected back to the trends list and a dialog is displayed telling you what happened.
* Fixed a visual bug in TankEngine related to the top 1px border of the toolbar (it used to be a dark line, it now matches the actual iPhone toolbar element).
* Updated the default loading image used for the fullscreen Trends application (accessed through the home screen shortcut) so that the toolbar matches the updated toolbar.