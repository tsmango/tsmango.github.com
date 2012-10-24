---
layout: post
title: "Mobile Twitter Trends Application"
alias: /post/180267415/mobile-twitter-trends-application/index.html
---

**Update:** [Version 2.1 has been released](http://thomasmango.com/2009/09/06/updated-mobile-twitter-trends-application-2-1).

Just over a month ago, I released a [mobile web application](http://trends.slicedsoftware.com) that used the API from [whatthetrend.com](http://www.whatthetrend.com) to present a list of recently trending topics on Twitter, along with their explanations. This little application turned out to be pretty handy, but for a while I've wanted to change it a bit. Most importantly, I wanted to have the front page of the application be the list of *currently* trending topics on Twitter.

<p class="image">
  <img src="http://thomasmango.com/images/2009/06/trends-2-0.jpg"/>
</p>

### Trends: Version 2

* The interface has been re-implemented from the ground up (actually the entire application was rewritten from scratch) and is now styled like a normal list-based iPhone application.
* When you first load the application, it shows you a list of the topics that are currently trending on Twitter.
* Clicking on a topic will bring you to that topic's page. The explanation as to why that topic is trending is pulled in from What the Trend? and displayed here.

I will be adding lists for the daily trending topics as well as the trending topics for the week - for now, only the current trending topics are available.

Please visit [Trends](http://trends.slicedsoftware.com) on your iPhone and add it to your home screen for the best experience.

### Sweat the Details

For this project, I used Noel Rappin's [TankEngine](http://github.com/noelrappin/tank-engine) plugin for the iPhone's user interface. I ended up having to hack it a bit so that it would run in fullscreen mode without breaking out into Safari when clicking links, but overall it was very enjoyable to use. You can run the Trends application in fullscreen mode by tapping the + button in MobileSafari and then tapping 'Add to Home Screen'. When you launch Trends from the new icon on your home screen, it will launch in fullscreen mode.

### heroku

In addition to wanting to update this application a bit, I've been really wanting a reason to try [heroku](http://heroku.com/) and I thought this was the perfect application to use as my test run. heroku is a service that allows you to deploy ruby applications quickly and easily. You know what? Saying it allows you to deploy ruby applications quickly and easily is an _understatement_ – heroku is, by far, the most wonderful service I have used in recent memory. I set out tonight to rewrite my mobile Trends application from scratch. Once I had a working version, I went to heroku not knowing anything of how it worked, signed up and in less than two minutes my application was deployed to heroku and running perfectly.

If you have a simple application that you're planning to host on your own server, I highly suggest taking a minute to deploy it over at heroku. It's free for small applications and I guarantee you will immediately see what an incredible service it is. I honestly can't say enough.

### Conclusion

I don't sleep enough and heroku is awesome.