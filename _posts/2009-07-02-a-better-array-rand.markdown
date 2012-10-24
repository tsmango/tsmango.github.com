---
layout: post
title: "A better Array#rand"
alias: /post/134370830/a-better-array-rand/index.html
---

I [released a new plugin today](http://github.com/tsmango/rand), called rand. This plugin expands on the `#rand` method ActiveSupport adds to Array.

As background, the `rand` method that Rails gives you, returns a single random value back from the array it's called on.

<script src='https://gist.github.com/1165329.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>&gt;&gt; [0, 1, 2, 3, 4].rand
=&gt; 2</code></pre>
</div>
</noscript>

This rand plugin overrides the `rand` method so that you can pass in an integer value that corresponds to how many random values from the array you actually want back.

<script src='https://gist.github.com/1165327.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>&gt;&gt; [0, 1, 2, 3, 4].rand(2)
=&gt; [4, 0]

&gt;&gt; [0, 1, 2, 3, 4].rand(4)
=&gt; [3, 0, 2, 1]</code></pre>
</div>
</noscript>

Additionally, the existing functionality is still in place so that if you don't pass in a parameter, it still gives you back a single random value.