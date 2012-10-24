---
layout: post
title: "Automatically Resizing a Textarea's Height"
alias: /post/191242481/automatically-resizing-a-textareas-height/index.html
---

Here is a simple Javascript method that can be used to automatically resize the height of a textarea based on the amount of content inside.

[Prototype](http://prototypejs.org) is used in this example.

[Click through](http://blog.slicedsoftware.com/post/191242481/automatically-resizing-a-textareas-height) if you don't see the code snippet below.

<script src='https://gist.github.com/189275.js?file=resize.html'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>&lt;!-- Automatically resize a textarea's height (like Facebook) --&gt;

&lt;textarea id=&quot;comment&quot; rows=&quot;1&quot; cols=&quot;50&quot;&gt;&lt;/textarea&gt;

&lt;script type=&quot;text/javascript&quot; charset=&quot;utf-8&quot;&gt;
  function autoResize(fieldId) {
    var length = $(fieldId).value.length;

    if(length &gt; 0) {
      $(fieldId).rows = Math.floor(length / $(fieldId).cols) + 1;
    } else {
      $(fieldId).rows = 1
    }
  }

  $('comment').observe('keyup', function(){autoResize('comment');});
&lt;/script&gt;</code></pre>
</div>
</noscript>