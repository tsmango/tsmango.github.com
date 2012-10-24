---
layout: post
title: "Using Partials as Reusable Inner Layouts"
alias: /post/156640294/using-partials-as-reusable-inner-layouts/index.html
---

You may find this pretty basic, but it's useful nonetheless.

Using partials in Rails is a wonderful example of code reuse. For example, you may have comments that use polymorphism so that you can attach comments to different models in your system. If you always want your comments to display using the same UI (and why wouldn't you) on not only Project Messages but also To Do items, you can create a `/comments/_comment` partial and render these partials from either the Project Message page or the To Do item page. But, we all knew that.

Sometimes, however, you may want to use a partial like an inner layout inside of your page's layout.

<script src='https://gist.github.com/162703.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>&lt;!-- /dashboard/_container - This partial is used as a template --&gt;

&lt;table id=&quot;dashboard&quot;&gt;
  &lt;tr&gt;
    &lt;td id=&quot;tabs&quot;&gt;
      &lt;!-- Links to different areas of the dashboard --&gt;
    &lt;/td&gt;
    &lt;td id=&quot;main&quot;&gt;
      &lt;%= yield %&gt;
    &lt;/td&gt;
  &lt;/tr&gt;
&lt;/table&gt;


&lt;!-- /messages/_inbox - This partial uses the dashboard _container as a wrapper --&gt;

&lt;% render(:layout =&gt; &quot;/dashboard/container&quot;, :locals =&gt; {:selected =&gt; 'inbox'}) do -%&gt;
  &lt;div id=&quot;messages&quot;&gt;
    &lt;%= render @messages %&gt;
  &lt;/div&gt;
&lt;% end -%&gt;</code></pre>
</div>
</noscript>

Above is a really simple example, demonstrating how you can use a partial as an "inner layout". Essentially this allows you to have a reusable partial with contextual, custom view code somewhere inside that reusable template - just like you'd use a regular page layout, except now you can have them scattered about your application.

Yes, this example in particular is really simple, but if you've never seen partials used this way, your brain is probably already cranking on how immensely useful this can be.