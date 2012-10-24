---
layout: post
title: "Subdomain Specific Sign In Pages - Stop The Madness"
alias: /2012/03/11/subdomain-specific-sign-in-pages-stop-the-madness/index.html
---

When I click "Sign In" on the front page of a site and I'm asked to enter my account name or subdomain so that I can be sent to "the correct sign in page", I cringe.

> The term "user-hostile" is defined as something that is "difficult to use, especially for an untrained user"<sup id="fnr-1"><a href="#fn-1">1</a></sup>.

Developers, please stop scoping users to accounts or subdomains. It's a terrible idea and causes all sorts of problems. It's bad enough we ask users to remember an email address (or worse, a username) and password, but now you're asking them to remember their account's subdomain? Why not ask them to perform calculus while you're at it?

Additionally, there are many circumstance when your user may have multiple accounts. If you want it so they can use the same email address under multiple accounts, just make it so that email address is attached to a single user and that user has permission to access multiple accounts!

By making this one change: ensuring that user accounts are unique to your application, rather than an account within your application, and then simply granting permissions between a user and one or many accounts, you won't be forced to ask them to first enter their account's subdomain before they can sign in.

If they go directly to their account's specific sign in page, great. But, let your users click a sign in link on your site's front page and be presented with an actual sign in form. After signing in, show them a list of accounts they have access to. From there, they can click through to the account they want to manage.

Please, stop the madness. Stop requiring your users to enter their account name before they sign in.

---

<ol>
  <li id="fn-1"><a href="http://en.wiktionary.org/wiki/user-hostile">On Wiktionary</a>, at least.<a href="#fnr-1">↩</a></li>
</ol>