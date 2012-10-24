---
layout: post
title: "A better Rails.cache.increment"
alias: /post/130075743/a-better-rails-cache-increment/index.html
---

I started working with `Rails.cache.increment` and `Rails.cache.decrement` today but quickly found that it didn't work as I planned. For example, it seemed that when calling these methods they would return the previous value rather than the newly incremented value. Additionally, if you decided to read directly using `Rails.cache.read` and the same key, it would always return nil despite being able to call `Rails.cache.increment` again. Another annoying thing is that if you tried to increment a value that wasn't in memcached, instead of defaulting to 1, it returned nil. Just all sorts of wacky stuff going on. I decided to just write my own.

<script src='https://gist.github.com/135972.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code># app/models/util/cache.rb

class Util::Cache
  def self.increment(key, amount = 1)
    if (value = Rails.cache.read(key)).nil?
      Rails.cache.write(key, (value = amount))
    else
      Rails.cache.write(key, (value = value + amount))
    end

    return value
  end

  def self.decrement(key, amount = 1)
    if (value = Rails.cache.read(key)).nil?
      value = 0
    else
      Rails.cache.write(key, (value = value - amount))
    end

    return value
  end
end</code></pre>
</div>
</noscript>