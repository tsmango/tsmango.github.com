---
layout: post
title: "A Simple .union Extension For ActiveRecord"
alias: /post/82183997/a-simple-union-extension-for-activerecord/index.html
---

I just wrote and [released my first rails plugin](http://github.com/tsmango/union), called union. It's absurdly simple and fairly naive. I wrote this plugin so that I didn't have to look at an ugly UNION I was running in a `find_by_sql`.

<script src='https://gist.github.com/1165362.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>ActiveRecord::Base.union(parts, options = {})</code></pre>
</div>
</noscript>

The first parameter, parts, is an array of hashes. Each hash is what you would normally send into a single find and represents each SELECT. All parts will be unioned together.

The second parameter, options, is a hash of remaining options to be applied to the UNION of the parts (ie: order, limit, offset).

A simple (and useless) example would be:

<script src='https://gist.github.com/1165363.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>User.union([{:conditions =&gt; ['name = ?', 'tom']}, {:conditions =&gt; ['name = ?', 'gary']}], {:order =&gt; 'created_at'})</code></pre>
</div>
</noscript>

This example produces the following SQL:

<script src='https://gist.github.com/1165366.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>(SELECT * FROM `users` WHERE (name = 'tom')) UNION (SELECT * FROM `users` WHERE (name = 'gary')) ORDER BY created_at;</code></pre>
</div>
</noscript>

Essentially you can do any union, but it's up to you to make sure you don't pass the wrong stuff in because it's a pretty dumb implementation.