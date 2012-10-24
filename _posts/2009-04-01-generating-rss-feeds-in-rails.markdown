---
layout: post
title: "Generating RSS Feeds in Rails"
alias: /post/91983824/generating-rss-feeds-in-rails/index.html
---

It's very easy to generate RSS feeds in Rails. In fact, if there is any possible benefit to having RSS feeds in your rails application, there is absolutely no reason you shouldn't take a few minutes and set them up.

These days, you just have to create a file like `:action.rss.builder` to go along with your `:action.html.erb` file. You don't even need a `respond_to` block in your action. As long as you have a route setup that knows how to handle additional formats, rails will automatically render your `.rss.builder` file rather than your `.html.erb` file if an `.rss` extension is at the end of the url.

For the basics of generating RSS feeds in rails, check out this [screencast](http://railscasts.com/episodes/87-generating-rss-feeds).

If you'd like to include media in your RSS feeds you should use the Yahoo media namespace xml definitions. To include this namespace in your feed you can use:

<script src='https://gist.github.com/1165347.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>xml.rss(:version =&gt; &quot;2.0&quot;, &quot;xmlns:media&quot; =&gt; 'http://search.yahoo.com/mrss/')</code></pre>
</div>
</noscript>

You can then generate a namespaced XML element like this:

<script src='https://gist.github.com/1165348.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>xml.media(:content, :url =&gt; &quot;some-image-url&quot;, :type =&gt; &quot;image/jpeg&quot;)</code></pre>
</div>
</noscript>