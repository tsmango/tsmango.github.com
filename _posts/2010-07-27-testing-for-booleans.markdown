---
layout: post
title: "Testing for Booleans"
alias: /post/866766857/testing-for-booleans/index.html
---

I just released a new plugin called [to_b](http://github.com/tsmango/to_b). Just like my other public projects on [github](http://github.com/tsmango), this plugin is extremely simple and is meant to help with something I've found very annoying: the lack of a to_b method.

Installing it in your Rails project will add a to_b method to NilClass, TrueClass, FalseClass, Fixnum and String. I've found this most useful when providing optional boolean parameters in API methods. I explain this situation a bit more in the README, which I'll embed below.

### README

A simple plugin that adds a .to_b method to various classes to aide in testing
for boolean values against variously typed objects.

### Why nil, false and true

If you look at the source, you'll note that nil.to_b returns nil, rather than
false. Additionally, a string like 'notaboolean'.to_b also returns nil. This
was done on purpose. I always ensure all the boolean columns in my database
are created with :null => false to avoid any issues relating to null values
in boolean fields. However, to_b returning nil in certain circumstances allows
me the flexibility when using this in conjunction with an API to accept nil,
false and true values for specific parameters.

An example of why I want the ability to test for nil, false and true is that
if a boolean parameter is left out I want my SQL to search WHERE true OR false,
if the parameter is set to true I want it to search WHERE true and if the parameter
is false, false. It is simply easier to have a scope like the following:

<script src='https://gist.github.com/1163935.js?file=example-scope.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>named_scope :with_tip, lambda {|value| value.to_b.nil? ? {} : {:conditions =&gt; {:tip =&gt; value.to_b}}}</code></pre>
</div>
</noscript>

Rather than explicitly searching WHERE true OR false, I simply leave out the
condition. When the value is true or false, I include the condition.

### Examples

<script src='https://gist.github.com/1163968.js?file=to_b-examples.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>&gt;&gt; true.to_b
=&gt; true

&gt;&gt; false.to_b
=&gt; false

&gt;&gt; nil.to_b
=&gt; nil

&gt;&gt; 'true'.to_b
=&gt; true

&gt;&gt; 'TRUE '.to_b
=&gt; true

&gt;&gt; 'false'.to_b
=&gt; false

&gt;&gt; 'F'.to_b
=&gt; false

&gt;&gt; 'random'.to_b
=&gt; nil

&gt;&gt; 1.to_b
=&gt; true

&gt;&gt; 0.to_b
=&gt; false

&gt;&gt; 5.to_b
=&gt; nil</code></pre>
</div>
</noscript>