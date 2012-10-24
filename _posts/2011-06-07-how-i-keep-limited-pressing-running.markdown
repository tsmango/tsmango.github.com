---
layout: post
title: "How I Keep Limited Pressing Running"
alias: /post/6301645915/how-i-keep-limited-pressing-running/index.html
---

Being the only developer of [Limited Pressing](http://limitedpressing.com), [The Old LP](http://theoldlp.com) and a few other services, I'm on-call 24 hours a day. If something breaks, no matter what time it is or where I am, I need to be able to react. I need to be able to quickly figure out what happened, what the damages are and how to get everything back up and running as quickly as possible.

With this in mind, there are three major areas that I think about and have invested in: monitoring, my "go bag" and an automated architecture. I'm sure there are many tools out there and others would have differing suggestions, but below is what I have in place, what I have with me wherever I go and what has worked for me so that I can make sure everything I'm responsible for, stays running.

### Monitoring

Without thorough monitoring, you can't expect to be able to keep your site online. I use and seriously endorse the following services to help with monitoring:

* [Clicky](http://getclicky.com) - This is a fantastic, realtime analytics service. Not only does it offer everything you'd expect from a Google Analytics type service, but statistics come in as they occur. Clicky will tell you how many people are currently on your site and, with Spy, will even let you see every action as it happens. If you aren't using a realtime analytics service like this, you are flying blind. The benefit of knowing exactly what's going on, at any given time, is immeasurable.
* [Pingdom](http://pingdom.com) - On it's own, Pingdom is extremely useful. Get an email, SMS or push notification if your site goes down. An uptime monitor like Pingdom is essential if you want to know when there is a service disruption.
* [PagerDuty](http://pagerduty.com) - Maybe it's just me, but between being a heavy sleeper and my iPhone only checking email every 15 minutes, Pingdom downtime emails are just not quite enough for me. With just Pingdom, I still found myself worrying. However, if you couple Pingdom with PagerDuty, you will never worry again. PagerDuty will call your cellphone within seconds of receiving an email from an uptime monitor, like Pingdom. Knowing that you aren't missing downtime notifications is extremely comforting.
* Clickylert - This is a custom service I built and haven't (yet?) released publicly. Clickylert polls Clicky for the number of people currently on one of my sites and detects significant, upward changes. When a large spike is detected, Clickylert emails me how many people are currently on the site along with how the traffic changed over the past hour. Additionally, Clickylert sends an email to PagerDuty, which calls my phone. If Pingdom is how I get notified when something very bad happens, Clickylert is what notifies me when something out of the ordinary is happening, which may or may not lead to something bad.

As you can see, there are few aspects to how I approach monitoring. At any given time, I want to know what's going on with my web services. From the analytics services I've used, Clicky knocks this one out of the park. However, simply being able to go in manually and look at what's going on, will not keep you from worrying all the time. An early warning system, like Clickylert, lets me know that something out of the ordinary is happening that could potentially be bad. An uptime monitor, like Pingdom, lets me know when something bad *has* happened. Lastly, PagerDuty informs me the instant one of my early warning or uptime services has something to tell me. When things matter, email, SMS and push notifications just do not cut it.

### What's in My "Go Bag"

If you know me, you probably also know that I live in the middle of nowhere and rarely go places. But for the times I do go out, I have certain things that I always take with me, even to the supermarket, that have made my life much easier. I never leave home without my laptop (a MacBook Air) and my iPhone. Additionally, I have my iPhone's "personal hotspot" (wi-fi tethering) enabled as well as an iMac at home that I can VNC into from my phone. I generally don't go places where I can't bring my laptop with me or, at the very least, leave it in my car, but if that does happen, having an iPhone with a few applications and an iMac at home, will most likely be all I need.

On my iPhone, I have the following applications:

* [Screens](http://www.edovia.com/screens) - A fantastic VNC application. Screens will get you into your iMac and let you do whatever you need to do from your home, development machine.
* [Prompt](http://itunes.apple.com/us/app/prompt/id421507115?mt=8) - Recently released from [Panic](http://panic.com), Prompt is a nice, simple SSH application.
* [ClickyTouch](http://clickytouch.com) - While certainly not a replacement for Clicky's main site, ClickyTouch is very useful for when you want to know how many people are on your site right now.

The one application I feel like I'm really missing from my phone, is an AWS application like [Elastics](http://itunes.apple.com/us/app/elastics/id418982422?mt=12). Elastics is a Mac menubar app that will give you the status of and direct access to every EC2 instance you have running. I've contacted the developers of Elastics and asked if they were thinking about building an iOS version, but haven't heard back yet.

I don't enjoy having to bring my laptop with me wherever I go, because it's a reminder that I'm always on-call and always being on-call means that something could go wrong at any time. But when it comes down to it, I would much rather pull over to the side of the road and open up my laptop, than be stuck somewhere without a way of dealing with a situation.

### Architecture & Automation

Invest in your architecture. Not necessarily with money directly, but with time. The more of your architecture you can automate and depend on, the better you'll sleep at night and the more quickly you'll be able to recover if something goes wrong. It seems like an obvious point, but I think a lot of people don't take this seriously enough at first. If monitoring will get you half the way, self configuring server instances and auto scaling will get you the rest of the way. Being a solo developer is not an excuse to not invest time in automation. In fact, being a solo developer is even more of a reason to invest the time.

I'm a big believer in [Amazon Web Services](http://aws.amazon.com). As someone who was never really great at server administration, AWS has really helped me wrap my head around a lot of concepts and has given me a great place to experiment with configuring and running various systems. If you're the only developer of your web service and don't want to or can't pay for a fully managed service like Heroku, you're going to eventually have to learn to configure and fix things. AWS is a great place to learn and practice because you can spin up as many fresh, clean installs of a server instance as you need to get things right and it won't cost more than a few dollars.

For my services, I developed self configuring AMIs. When an instance launches, it pulls down the latest configuration files for the role it's going to play, checks out any code that needs to be run and connects itself to the rest of the infrastructure. When you combine self configuring EC2 instances with AWS' [auto scaling](http://aws.amazon.com/autoscaling) and [load balancing](http://aws.amazon.com/elasticloadbalancing) (or with your own load balancer), you've already gone a long way in ensuring your service stays up and running. When most people hear "auto scaling", they think about when you get a rush of traffic and you automatically fire up additional server instances to keep up. Although that's the main benefit of auto scaling, another benefit is simply keeping your service online. If an instance dies for some reason, a new instance can automatically start up and take its place. Additionally, having all of your instances behind a load balancer (even just a single instance), means that application servers can come and go without affecting connectivity to your service.

To take the idea of auto scaling further, you have to design your systems to be loosely coupled. For most web services, if you remove data from the equation (into [Amazon RDS](http://aws.amazon.com/rds), for example), you're simply left with processing. For Limited Pressing, we use a combination of RDS, a background queue and EC2 instances that can be turned on and off at any time, without affecting anything. When you can think of EC2 instances running your web application as disposable processing servers, auto scaling is no longer just about adjusting for traffic, it's about keeping your service online, no matter what happens.

### Conclusion

Werner Vogel, Amazon's CTO, famously said: "everything fails, all the time". You need to design for failure and you need to be ready to react when something goes wrong. For me, as a single developer with limited resources, that means thorough monitoring, always being connected and being able to rely on an automated architecture.