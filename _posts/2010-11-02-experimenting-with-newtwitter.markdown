---
layout: post
title: "Experimenting with #newtwitter"
alias: /post/1466659963/experimenting-with-newtwitter/index.html
---

When Twitter recently launched their new site, [#newtwitter](http://twitter.com/newtwitter), I was delighted to learn it was actually an API client. As someone who builds web applications and works on APIs myself, it was inspirational to see this all done with HTML and Javascript. Needless to say, when my account gained access to #newtwitter, I fired up my Web Inspector and started digging around with the console – you can learn a lot with the console's tab completion.

After experimenting for a bit, I whipped up some quick Javascript that appends a "?" link to each trend. When clicking the "?" link, a standard #newtwitter dialog opens up and an explanation of the trend, fetched from [What The Trend](http://whatthetrend.com), is displayed.

<p class="image">
  <img src="http://thomasmango.com/images/2010/11/trend-explanation.png"/>
</p>

One interesting thing to remember when working with the trends list on #newtwitter is that trends update every once in a while. Simply executing this script once won't do the trick. I decided to let jQuery's [live](http://api.jquery.com/live/) function handle that issue.

Check out the source below if you're interested in how I did it:

<script src='https://gist.github.com/660636.js?file=trends-explained.js'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>jQuery(function(){
  var TrendExplanation = {
    dialog:null,
    trend:null,

    // Open a dialog with the explanation of the given trend.
    open:function(trend) {
      this.dialog = new twttr.widget.Dialog({draggable: true});
      this.trend = trend;

      this.setup().explain();
    },

    // Set the dialog's title and show it.
    setup:function() {
      this.dialog.$title.html('About &amp;quot;' + TrendExplanation.trend + '&amp;quot;');
      this.dialog.$find('.twttr-dialog-content').html('&lt;p&gt;Loading...&lt;/p&gt;');
      this.dialog.show();

      return this;
    },

    // Fetch an explanation for this TrendExplanation's current trend.
    explain:function() {
      $.ajax({
        url: 'http://www.whatthetrend.com/api/trend/getByName/' + encodeURIComponent(TrendExplanation.trend) + '/jsonp?callback=',
        dataType: 'jsonp',
        success: function(data) {
          if(data['api']['trend'] &amp;&amp; data['api']['trend']['blurb']['text'] != '') {
            TrendExplanation.update(data['api']['trend']['blurb']['text']);
          } else {
            TrendExplanation.update('Unable to find details about that trend.');
          }
        },
        error:function(data) {
          TrendExplanation.update('There was a problem attempting to find details about that trend.');
        }
      });

      return this;
    },

    // Update the dialog for this TrendExplanation with the given explanation.
    update:function(explanation) {
      this.dialog.$find('.twttr-dialog-content').html('&lt;p style=&quot;margin-top:5px;&quot;&gt;' + explanation + '&lt;/p&gt;');
      this.dialog.$find('.twttr-dialog-content').append(footer);

      return this;
    },

    // Hide this TrendExplanation's dialog.
    hide:function() {
      this.dialog.hide();

      return this;
    }
  };

  // When moving over this trend-item, show the explain-trend link.
  // If a Trend doesn't have an explain-trend link, add one.
  $('.trend-item').live('mouseover', function(index) {
    if($(this).find('.explain-trend').length == 0) {
      $(this).append($(&quot;&lt;a/&gt;&quot;, {
        &quot;href&quot;:   &quot;http://whatthetrend.com/trend/&quot; + encodeURIComponent($(this).find('.trend-link').text()),
        &quot;class&quot;:  &quot;explain-trend&quot;,
        &quot;target&quot;: &quot;_blank&quot;,
        &quot;text&quot;: '?'
      }));
    }

    $(this).find('.explain-trend').show();
  });

  // When moving off of this trend-item, hide the explain-trend link.
  $('.trend-item').live('mouseout', function(index) {
    $(this).find('.explain-trend').hide();
  });

  // When clicking an explain-link, find and display this Trend's explanation.
  $('.explain-trend').live('click', function() {
    TrendExplanation.open($(this).parent().find('.trend-link').text());

    return false;
  });

  // Build a footer with details about this script that can be attached to the dialog showing a Trend's explanation.
  var footer = $(&quot;&lt;p/&gt;&quot;).css({'border-top': '1px solid #eeeeee', 'font-size': 'smaller', 'margin-top': '10px', 'padding-top': '5px'})
    .append($('&lt;span&gt;Explanation from @&lt;a href=&quot;/#!/whatthetrend&quot;&gt;whatthetrend&lt;/a&gt;.&lt;/span&gt;'));
});</code></pre>
</div>
</noscript>