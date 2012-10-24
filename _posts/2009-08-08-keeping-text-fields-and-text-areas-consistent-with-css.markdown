---
layout: post
title: "Keeping Text Fields and Text Areas Consistent With CSS"
alias: /post/158650936/keeping-text-fields-and-text-areas-consistent-with-css/index.html
---

Let me just start by saying that I don't claim to be good at design at all, but as I don't work with a designer, I have had to fend for myself over the years. I'd say I "get by" in the design department by sticking to things that aren't overly complicated. It's probably also worth noting that I can be a perfectionist at times. I care a lot about the code I produce - whether it's 5 lines of code that can be written more simply or a few pixels lining up better (admittedly, I'm way better at refactoring code than I am at interfaces).

What I'd like to explain is a very simple thing I do to keep my text fields and text areas looking consistent with just a couple lines of CSS.

By default, most browsers like Safari and Firefox render text fields with a simple 3D type depth. It makes the field stand out. Text areas, on the other hand, are usually rendered without that 3D type effect. They are given a simple, flat border. This is what 2 text fields look like compared to a text area, by default, without any CSS:

<p class="image">
  <img src="http://thomasmango.com/images/2009/08/text-fields-no-border.png"/>
</p>

The first step to keeping things consistent looking is to simply set the border of text fields and text areas with CSS. Now both widgets are at a level playing field.

<p class="image">
  <img src="http://thomasmango.com/images/2009/08/text-fields-border.png"/>
</p>

Just setting the border is a nice first step, but that slight 3D depth given to text fields by default is actually pretty useful to indicate to the user that she can click and type into the area. To do this, you can simply set the border-top of the text fields and text areas to be slightly darker than the border you set in the last step.

<p class="image">
  <img src="http://thomasmango.com/images/2009/08/text-fields-border-with-depth.png"/>
</p>

I suspect that most users looking at this form wouldn't even think twice about this subtle tweak, and that's the beauty of it. It's so simple yet still does such a great job of making your forms look way, way sexier.

Sample CSS:

<script src='https://gist.github.com/1165286.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>.textfield, textarea {
  border: 1px solid #c9c2c1;
  border-top: 1px solid #999999;
}</code></pre>
</div>
</noscript>

Maybe the word for me is neurotic.