---
layout: post
title: "MySQL Isn't Very Sensitive"
alias: /post/77554324/mysql-isnt-very-sensitive/index.html
---

When writing SQL conditions in MySQL, using an equal sign is case insensitive. For example:

<script src='https://gist.github.com/1165375.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>SELECT * FROM comments WHERE thread_id = '2A';</code></pre>
</div>
</noscript>

Will return results where thread_id actually equals 2a and 2A. To make this case sensitive, you can use `LIKE BINARY`:

<script src='https://gist.github.com/1165376.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>SELECT * FROM comments WHERE thread_id LIKE BINARY '2A';</code></pre>
</div>
</noscript>

But what happens when you want to use IN? For example:

<script src='https://gist.github.com/1165377.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>SELECT * FROM comments WHERE thread_id IN ('2A', '2B', '2C');</code></pre>
</div>
</noscript>

This would return where thread_id actually equals 2a, 2A, 2b, 2B, 2c and 2C. This is no good because I need it to be case sensitive. You could do something like:

<script src='https://gist.github.com/1165379.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>SELECT * FROM comments
WHERE thread_id LIKE BINARY '2A'
OR thread_id LIKE BINARY '2B'
OR thread_id LIKE BINARY '2C';</code></pre>
</div>
</noscript>

But this is a problem when you want dynamically built this query using an ActiveRecord find method like:

<script src='https://gist.github.com/1165380.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>Comment.find(:all, :conditions =&gt; ['thread_id IN (?)', ['2A', '2B', '2C']])</code></pre>
</div>
</noscript>

I haven't found a solution yet, but I hope to soon.