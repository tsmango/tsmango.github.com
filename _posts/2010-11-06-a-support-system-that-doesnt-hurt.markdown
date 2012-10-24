---
layout: post
title: "A Support System That Doesn't Hurt"
alias: /post/1493591618/a-support-system-that-doesnt-hurt/index.html
---

We opened [Limited Pressing](http://limitedpressing.com) to the public a year ago and up until last week, we've been accepting support requests to an email address we provided on the site as well as a public forum. As our traffic grew, handling support requests this way became unbearable. Often, a request involves some discussion internally before responding. To do this, we'd forward the email to each other to discuss it and when we replied, we'd CC each other. It was tiresome and unwieldily. It was also easy to lose track of who we were talking to because we didn't have any context, just an email address.

Well, that changed last weekend. We said enough was enough. We talked for a few minutes Sunday morning about what we wanted out of a support system and by 10pm the public forum was gone, the support email address had an auto responder and we had a brand new support system in place. And what I find most interesting about the whole thing is that we've had more support requests this week than any previous week in the past year. Thankfully, every one of those requests was a pleasure to deal with because of the new system.

### Requirements

When designing the new support system, we had a few must haves:

* The word "tickets" is too technical for most people.
* You shouldn't need an account to submit a support request.
* You should receive email notifications to confirm we received your request and whenever there's an update to your request.
* Staff members should receive email notifications whenever someone submits a request and whenever a request is updated by someone else.
* Staff members should be able to have an internal, private discussion right on that support request's page. Context is everything.
* Each support request should have as much information we know about the person asking the question so staff members don't have to hunt around for it.

There were a lot of other ideas we threw around that would have been neat, but not worth the time. For example, it would be nice to know when other staff members were currently looking at (or typing a response to) the same support request you were looking at. It would be useful, but we wanted to get the new support system up and running as quickly as possible and this just didn't make the cut.

Another feature that didn't make it in was the ability for a staff member to assign a question to another staff member. A formal system would be nice, but since we could leave staff only notes on each support request, the same basic thing could be handled without a separate feature.

Frequently asked questions and response templates are a couple of other features we were thinking about implementing. We may still implement these, but were they necessary on the first iteration? Definitely not.

### 12 Hours Later

The system ended up being extremely simple and did exactly what we needed.

* Users, whether they're logged in or not, ask us questions.
* Every question has a unique URL that isn't guessable. The URLs aren't completely secure, but they're "secure enough".
* Staff members can have a conversation about the question right on the question page.
* Everyone gets notified when there's something new to see.
* Questions automatically change state between Open, Pending and Solved depending on who answers what and when.

### More Support Requests Than Ever Before

With our old setup, an open support email address and a public forum, we received a decent amount of support requests. When moving to the new question system, we setup an auto responder on the support email address that pointed people to the new system and we completely removed the forums. The result? Within 12 hours, people started using the new system. No instructions, no anything. We've had about 5x our usual load of support requests with the new setup this week and we've spent about 1/4 of the time handling them. The new setup is a complete success.

So why the sudden interest in Limited Pressing support? I have a theory. A lot of people feel like they're being a bother emailing a support email address, so they just don't. And the support forums? People don't want to ask personal questions on a public forum and people don't want to ask questions they think may make them look stupid on a public forum. People don't want to be a bother and don't want to look stupid. It's that simple. Our private questions area solved both of these issues.

Additionally, we wanted our support system to feel friendly. Rather than "creating a support ticket", you "ask a question". Here's what the ask a question form looks like:

<p class="image">
  <a href="http://thomasmango.com/images/2010/11/new-question.png">
    <img src="http://thomasmango.com/images/2010/11/new-question.png"/>
  </a>
</p>

It's a minor difference that I think changes how people approach it. It's easy to ask a question. It's more of a commitment to "open a new support ticket".

Also, unlike support emails and forum topics, questions don't require subjects. Instead, you choose from a list of several general categories:

* I have a question before getting a store.
* I'd like to request a new feature.
* I'm confused about how something works.
* I think something is broken.
* Other.

### Conclusion

Without narrowing the scope of the project, I would have spent too long building the new support system and it would have included a ton of bells and whistles we didn't need. It just wouldn't have been worth it. With the narrowed scope, I only invested a single day and we still ended up with a vastly improved way to manage support requests.

Always reduce a project's scope as much as you can and see how it works in production. You can add things later if you really need to, but if you're lucky, you won't.