---
layout: post
title: "A Cacheable Hash That Stays Synced"
alias: /post/182158868/a-cacheable-hash-that-stays-synced/index.html
---

Rails.cache freezes values in hashes that are cached directly. [CacheableHash](http://github.com/tsmango/cacheable_hash) is a wrapper for Hash objects that prevents that from happening. Changes to the Hash being wrapped are automatically persisted back to the cache store so that everything stays in sync.

You can clone (or fork) my new MIT licensed plugin over at [github](http://github.com/tsmango/cacheable_hash).

### Example

An instance of CacheableHash is used like any other hash. A cache key is the only thing required.

<script src='https://gist.github.com/1164097.js?file=example-irb.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>&gt;&gt; fruit = CacheableHash.new('fruit_hash_key', :hash =&gt; {:apple =&gt; 'red', :banana =&gt; 'yellow'}, :expires_in =&gt; 1.week)
&gt;&gt; fruit[:banana]
=&gt; &quot;yellow&quot;</code></pre>
</div>
</noscript>

Changes are automatically persisted.

<script src='https://gist.github.com/1164100.js?file=example-irb.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>&gt;&gt; fruit = Rails.cache.read('fruit_hash_key')
&gt;&gt; fruit[:apple]
=&gt; &quot;red&quot;

&gt;&gt; fruit[:apple] = 'green'

&gt;&gt; Rails.cache.read('fruit_hash_key')[:apple]
=&gt; &quot;green&quot;</code></pre>
</div>
</noscript>

### About Synchronicity

Whenever a method is called on an instance of CacheableHash, that instance automatically writes itself back to the cache store. Often, this is unnecessary. If you call .keys, for example, there is no reason to write back to the cache store. Only when reading a value from the hash using `[]`, is the copy in the cache store not specifically updated. But, rather than worrying about the specific methods that change the contents of the hash, it's safer to just update the instance in the cache store whenever not specifically reading values.