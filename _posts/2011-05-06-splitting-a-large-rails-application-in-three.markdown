---
layout: post
title: "Splitting a Large Rails Application in Three"
alias: /post/5263131952/splitting-a-large-rails-application-in-three/index.html
---

After weeks of planning and a month of development, we finally split [Limited Pressing](http://limitedpresing.com)'s free auction, trading and collecting services out into a separate site last week. Although I have been involved in a couple of "big rewrites" before, this project was the most complicated I've ever done. It's one thing to rewrite a large, internal application or an entertainment application, but Limited Pressing is a large e-commerce platform that people depend on. People are constantly buying and selling things, so downtime had to be minimized and everything really had to go just right.

### A Brief History of Limited Pressing

Before going into details about splitting the application itself, I should back up and briefly explain how Limited Pressing evolved over time. LP, an e-commerce platform catering to the music industry, has been in development for about two years. The original idea was to be something of an eBay alternative for independent music. We had three major points we wanted to hit: auctions, trading and stores. We launched first with auctions and trading and immediately went to work on stores, the only feature we were going to charge for.

### The Need to Separate Stores & Community

After releasing stores to the general public, we began to get a lot of interest in them. We had solved a lot of issues people had with other store platforms and provided a great set of features specific to labels, bands and musicians. Unfortunately, it became clear that our free auction and trading services were distracting from our stores service. A lot of people were confused as to if we were a store platform or a community marketplace. We tried to be both for a while, but these two aspects clashed more and more. Additionally, users of the free services didn't really want to be bothered with stores and it was hard for people looking for a store to make it past our other services.

In the end, we decided that for both aspects to be as good as they could be, we had to move our free auction and trading services out into a separate site completely. This new site is called [The Old LP](http://www.theoldlp.com).

### Extracting Authentication & Being an OAuth Provider

Luckily, there was very little overlap between stores and community features. There were a few low level utilities related to currency and region support and then there was our user and authentication system, but otherwise, things could be clearly separated. Without this minimal overlap, we may never have be able to go down this road.

The first thing I did was duplicate the codebase into a new application. I then began to delete every controller, model and view that had nothing to do with that application. I removed auctions, deals, collections, messages, etc. from Limited Pressing and I removed stores and all store related features from the new application, The Old LP.

After removing features, I completely tore out the authentication systems, which was built on top of Authlogic. Like many Rails developers, I was lured into using a bloated authentication system. I've tried a number of them including [Authlogic](https://github.com/binarylogic/authlogic) and [Devise](https://github.com/plataformatec/devise). Unfortunately in my experience, I feel like these authentication systems always end up either being overkill or requiring too much overriding and customization that it makes no sense to bother using them. Because one of our requirements was to have the same logins across both applications, I took this as a chance to get rid of Authlogic. I ripped it all out and started building another application, called [Multipass](http://multipass.limitedpressing.com).

Multipass is my favorite part of this project. Very simply, Multipass started with the Limited Pressing database. I removed every table, except the users table and then removed all application specific columns from the users table and boiled it down to just user accounts. My inspiration for Multipass was [37signal Accounts](http://37signals.com/accounts): a secure single sign on service that works across all of our applications.

I'm not sure how 37signals went about doing cross application authentication through their accounts system, but with Multipass, I decided to make it an OAuth provider. To make this happen, I started with [oauth-plugin](https://github.com/pelle/oauth-plugin). This is a fantastic library, but there were a few things I wanted to avoid, which meant I had to make a number of changes to what oauth-plugin gives you by default.

For now, I disabled the ability for non-administrator's to create client applications. Although we don't currently want anyone to be able to create an OAuth Client Application and use our login system on their own, this is something we may allow in the future. We have a lot of Limited Pressing IDs and there may be other music related sites and services that would find it useful to piggy back on our authentication system at some point.

The bigger change was that we didn't want users to have to *allow* access to their account when signing into Limited Pressing or The Old LP (or any future application of ours). Although the idea of granting access to your account has become more mainstream because of Twitter and Facebook, I don't think everyone gets it yet. It's confusing and scary for people who don't understand what's going on.

I decided to implement a similar authentication flow to Twitter's "Sign in with Twitter" [/oauth/authenticate](http://dev.twitter.com/pages/sign_in_with_twitter) flow. Normally, every time a user attempts to log into a client application, that user would have to *allow* the third party application to use your account - this is how oauth-plugin works by default. However, "Sign in with Twitter" only requires a user to allow the third party application once. All subsequent logins skip the allow / deny process and send the user right back to the third party client. I achieved this by first modifying the generated RequestToken model. Rather than always generating a new access token on every sign in (oauth-plugin's default), I now first check if a valid token exists for that user and client application. If one does exist, I return that and skip right over the allow / deny request.

Although this helped a lot in that users wouldn't have to re-allow every time they signed in, this wasn't good enough. I already know that Limited Pressing, The Old LP and any other applications of ours are trustworthy, so why should I ask the user to grant access to the account? I modified my new /oauth/authenticate style flow further to detect whether or not the client application that's requesting access was trusted or not (I define a trusted application as one that was created by and tied to an administrator). If the application is trusted, not only do I skip all subsequent  allow / deny requests when a valid access token already exists, but I even skip the initial request when a new access token needs to be generated and I automatically allow access.

By implementing the concept of trusted applications in addition to the Twitter style "Sign in with Twitter" authentication flow, this allows Limited Pressing and The Old LP to send a user off to Multipass for authentication, but does not require that user to specifically grant access to our trusted applications. If we end up allowing third parties to create client applications in the future, they will not be inherently trusted and will require the user to allow the request for access.

On the client side of things, I wrote a custom OAuth based strategy for the excellent [OmniAuth](https://github.com/intridea/omniauth) gem. This allowed me to quickly and easily tie into our Multipass authentication system. I then removed duplicate user fields from the client applications and wrote a plugin to automatically pull fields that exist in Multipass accounts from Multipass directly. That means if you load up an instance of a User model in Limited Pressing and call `first_name`, `last_name`, `email_addreess`, etc., those calls automatically pass through that User and hook back into Multipass for the current values. This means not only are we not duplicating data across applications, but all existing code in each application that called those values on user objects wouldn't have to be changed. Additionally, all objects in each application still point to the local users table and use those ids for foreign keys, but every user also has a `uid` that represents the associated Multipass user.

By turning Multipass into an OAuth provider, not only do we now have a secure, single sign on system that can be used across all of our current and future related web applications, but we also now have a proper authentication system that can be used for a possible, future API.

### Maintainability & Our Support System

Aside from the business related decisions in splitting Limited Pressing into two services, there were also benefits in terms of future maintainability. Limited Pressing's codebase is roughly 2 years old and it had numerous customer facing features, in addition to authentication, administration tools and our custom help and support system.

With the launch of The Old LP, I removed authentication from the mix and built Multipass. However, our custom help and support system<sup id="fnr-1"><a href="#fn-1">1</a></sup> is still baked right into Limited Pressing. I plan to continue down the path of reducing the complexity of each application by moving our support system into it's own, separate application.

Looking past the obvious benefits of smaller, more focused codebases, splitting up Limited Pressing has other advantages. First, our services are now decoupled from each other. If our store platform does not depend on our auction system, something affecting our auction system will no longer affect our store platform. Next, breaking LP up makes it easier to upgrade everything to Rails 3 in pieces. Limited Pressing and The Old LP are still running on Rails 2.3.11, but Multipass is a Rails 3 application and when removed from LP, our support system will also be running on Rails 3. I will be able to upgrade LP and TOLP to Rails 3, individually, as time permits.

### Final Thoughts

Although this was a big undertaking, a lot of the work was in planning, testing and deployment of the three resulting applications. I ran through tests in all three systems and audited every bit of code to ensure everything would continue working properly after being separated. I spent days developing and rehearsing a deployment strategy. I took snapshots from our production database and wrote a detailed plan for our period of scheduled maintenance. During maintenance the three new applications would be deployed with their databases starting as duplicates of the production LP database. Each database was then stripped down to the necessary tables and modified to support changes to that specific application. I even timed myself and figured out what operations could be done at the same time to shorten the maintenance window. For example, I ended up deploying each application while restoring and migrating the next, depending application's database to minimize downtime.

But in the end, despite all of my planning, there were still some bugs that made it into production - something I knew could not be avoided no matter how much preparation was done. I spent the day of our deployment putting out minor fires here and there. And now that a week has passed and I can reflect on the entire project, I'm incredibly relieved it's over, but also extremely excited about our newly de-coupled applications. It was absolutely worth the effort.

---

<ol>
<li id="fn-1">This is essentially a custom built support system, like <a href="http://tenderapp.com">Tender Support</a>, only less polished. At some point we may just move to Tender, but for now, this works for us and only had an upfront cost of a weekend or so to build. <a href="#fnr-1">&#8617;</a></li>
</ol>