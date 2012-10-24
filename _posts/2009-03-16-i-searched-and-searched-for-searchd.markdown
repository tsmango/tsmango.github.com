---
layout: post
title: "I Searched and Searched for searchd"
alias: /post/86949087/i-searched-and-searched-for-searchd/index.html
---

For a while now I've been working on a really long and detailed post comparing the different search systems available for rails projects. I just deleted the draft.

I deleted it because I realized there really isn't anything to compare. If you don't **need** live updated search results and you can live with an index that's updated every 15 minutes or more, just use Sphinx. Sphinx with [Thinking Sphinx](http://ts.freelancing-gods.com) by [Pat Allan](http://twitter.com/pat) is simply the most elegant, full featured and most importantly, **stable** search system available for rails today. We've been using it over at [gawkk](http://gawkk.com) for months now and haven't had a single unhappy moment since we switched from Solr.

SQL searches are slow. Ferret is crap in production. Solr is almost as unstable as Ferret in production and will bring down your database with unnecessary connections every time a damn object is instantiated. Sphinx is rock solid and fast as hell.

I will only offer two lines of proof:

<script src='https://gist.github.com/1165358.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>total 1133325 docs, 266596694 bytes
total 117.702 sec, 2265011.00 bytes/sec, 9628.75 docs/sec</code></pre>
</div>
</noscript>

Over a million documents in 117 seconds. Try and do that with anything other than Sphinx. I dare you.