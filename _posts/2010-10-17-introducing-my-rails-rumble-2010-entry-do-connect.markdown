---
layout: post
title: "Introducing My Rails Rumble 2010 Entry: do*connect"
alias: /post/1339666686/introducing-my-rails-rumble-2010-entry-do-connect/index.html
---

My entry to [Rails Rumble 2010](http://railsrumble.com) is a service called [do\*connect](http://thomasmango.com/projects/doconnect). If you don't know what the Rails Rumble is, their site explains it as:

> The Rails Rumble is a kickass 48 hour web application development competition. As a contestant, your team gets one caffeine-fueled weekend to design, develop, and deploy the best web property that you can, using the awesome power of Ruby and Rails.

My application, do\*connect, gives you a simple way to build and grow a third party developer community around your service. I decided on this project because I feel that developer communities are always hobbled together. Google Groups are often used as a mailing list, sometimes [Tender Support](http://tenderapp.com) is used for documentation (but usually just non-developer support), but many times services have to roll their own.

Just look at [SimpleGeo](http://simplegeo.com/docs/), [Foursquare](http://groups.google.com/group/foursquare-api) and [Gowalla](http://gowalla.com/api/docs). Everyone does a great job hosting their documentation, but it shouldn't be so time consuming to start a developer community around your web service. I hope that with do\*connect, it won't be.

Regardless of the outcome of Rails Rumble 2010, I plan to continue expanding on do\*connect. I have a handful of additional features I'd like to implement, including some premium ones. I haven't decided on a cost yet for the premium service, but it will definitely support custom domains and a customizable appearance.

The free version will continue to live on and I expect to add features like a status dashboard (another developer-facing resource that everyone builds themselves – see: [Twitter](http://dev.twitter.com/status), [GitHub](http://status.github.com/), [37signals](http://status.37signals.com/) and [SimpleGeo](http://status.simplegeo.com/)), announcements and extending discussions to act like a mailing list.

One feature I'd like to highlight briefly, that isn't shown in depth in the below screencast, is the permissions system. Communities can be open, closed or private. Open communities are publicly visible but require you to join to post. Closed communities aren't publicly visible, but anyone can join. Private communities aren't publicly visible and memberships must be approved by Community Managers. Additionally, Community Managers can promote any other member to be a Manager, giving them the ability to create Pages and Documentation as well as delete Pages, Documentation and Discussions. I've always been a fan of simple permissions and I think what do\*connect supports is a nice balance between flexible and simple.

Here's a quick screencast to explain do\*connect:

<iframe src="http://www.screenr.com/embed/e7D" width="650" height="396" frameborder="0"> </iframe>