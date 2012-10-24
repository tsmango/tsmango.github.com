---
layout: post
title: "Resque and Very Fast Jobs [Spoiler: Avoid Them!]"
alias: /post/1610864640/resque-and-very-fast-jobs-spoiler-avoid-them/index.html
---

Although I suspect most people using Resque already know to avoid this, I thought I would write about an experience I had with Resque earlier this week in the hope that someone will benefit from what I learned.

### Background

We recently launched version 2.0 of [Firefly](http://thomasmango.com/projects/firefly) (my day job). With 2.0, came a completely re-written backend revolving around Twitter's new [Site Streams API](http://dev.twitter.com/pages/site_streams). Firefly 2.0 is focused on seeing where your friends are. It does this by collecting tweets with location information (including geo tagged tweets, tweets with photos that are geo tagged and third party check-in service tweets like Foursquare, Gowalla and many others). Once you sign into Firefly's iPhone app or website, you're added to one of our available Site Streams and we start receiving and processing your [home_timeline](http://dev.twitter.com/doc/get/statuses/home_timeline) in addition to various other messages that come through a Site Stream connection.

### Backlog

A very important thing to remember when working with any of Twitter's streaming APIs is that you must read from the stream as quickly as possible. If you don't read the stream fast enough, your connection will be terminated. If your connection keeps getting terminated because you aren't reading fast enough, they may block your access. With this in mind, I decided to use our existing Resque background processing setup. I took every item that came through the site stream and queued it up to be processed by a Resque worker later. I figured, "most of these items we ignore so the job will just happen instantly and it won't be a big deal, plus we get to read from the stream instantly because we aren't doing any work inline". If you're shaking your head right now, you already know where this is going.

Here's what I naively forgot to consider: the length of time it takes to process a job != the amount of time it takes to process the logic you wrote inside of that job. What's missing from the equation? **The overhead in processing the job!** It takes time for a Resque worker to start and stop processing a job and as it turns out, it takes enough time that when we hit a certain threshold of active users on Firefly, our queue started growing in size at an insane rate.

<p class="image">
  <a href="http://thomasmango.com/images/2010/11/resque-jobs.png">
    <img src="http://thomasmango.com/images/2010/11/resque-jobs.png"/>
  </a>
</p>

We peaked at close to 60k jobs in the queue before I could shut our Site Stream connections down, start investigating the issue and work on a fix.

### Solution

When I realized what was going on, it turned out that there was an obvious fix: reduce the number of unnecessary jobs being queued into Resque. Because of the nature of Firefly, we only end up processing a small subset of tweets (ones that we can collect location information on). Almost every other tweet and streaming message just gets passed over and these are the streaming items we don't want to queue to be processed later. The key to doing this is to remember that these streaming items still need to be processed as fast as possible to avoid slowing down the reading of the Site Stream connections.

First, I ensured that we never went to the database when determining whether or not a streaming item was something we wanted to queue. Next, I wrote a set of rules, specific to Firefly, that would recognize and reject any streaming item we didn't care about. And lastly, I ordered the rules so that the the fastest to process were first (each rule can reject a streaming item so if the most expensive rules were last, we always spend the least amount of time to reject an item).

### Conclusion

If a job will run so fast that it can be executed inline, don't queue it. It's that simple. Just be conscious of every job you're creating.

It's embarrassing to admit that I let this through to production, but I did. The important thing is that I learned to be very aware of how long a job is really going to take and whether or not it's worth it to queue it in the first place. I also learned that I need to start monitoring queue size and setup notifications if the queue continues to grow in size, but that's a story for another day.