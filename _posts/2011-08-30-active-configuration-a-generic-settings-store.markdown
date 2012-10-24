---
layout: post
title: "ActiveConfiguration: A Generic Settings Store"
---

[ActiveConfiguration](http://github.com/tsmango/active_configuration) is a Rails engine that exposes a generic settings store to ActiveRecord models. Made for very configurable applications, it allows you to avoid implementing specific ways to store settings for each model that needs such a configuration. If your application isn't very configurable, ActiveConfiguration isn't what you want.

For example, if your application had a `Category` model that only had a configurable `sort` attribute, ActiveConfiguration would be overkill. Rather, you would just read and write values using a `sort` column and restrict the allowed values using something like `validates_inclusion_of`.

However, if your `Category` model was more flexible in its configuration, you may want a `sort` setting, a `limit` setting and multiple `price_filter` settings that can be configured by your end user. Without ActiveConfiguration, you would have to develop a way to store and validate these settings for this specific scenario. The `sort` and `limit` settings are simple but because `price_filter` can accept multiple rules, you'd have to set up an additional model. Still, this isn't really an issue when you're dealing with just a single configurable model. When you're dealing with many, things tend to get messy.

With ActiveConfiguration, all of your settings, even for `price_filter`, can be stored in a generic way. ActiveConfiguration provides a place to store settings for each of your models and even handles validation when you restrict the allowed values or format of an option.

### Source

The source for ActiveConfiguration is [available at GitHub](http://github.com/tsmango/active_configuration).

### Installation

Add the following to your `Gemfile`:

<script src='https://gist.github.com/1182604.js?file=install-gem.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>gem 'active_configuration'</code></pre>
</div>
</noscript>

Generate the migration for the `settings` table:

<script src='https://gist.github.com/1182604.js?file=generate.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>rails g active_configuration:install</code></pre>
</div>
</noscript>

Migrate your database:

<script src='https://gist.github.com/1182604.js?file=migrate.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>rake db:migrate</code></pre>
</div>
</noscript>

### Example Configuration

<script src='https://gist.github.com/1182604.js?file=category.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>class Category &lt; ActiveRecord::Base
  configure do
    option :sort do
      default  'alphabetical'
      restrict 'alphabetical', 'manual'
    end

    option :limit do
      format 'fixnum'
    end

    option :price_filter do
      format    'float'
      modifiers 'eq', 'lt', 'gt', 'lte', 'gte'
      multiple  true
    end
  end
end</code></pre>
</div>
</noscript>

After installing ActiveConfiguration, the `#configure` block is available to
every ActiveRecord model. If the `#configure` block is defined with a valid
configuration, additional methods are made available on the model.

### Example Usage

<script src='https://gist.github.com/1182604.js?file=example.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>&gt;&gt; category = Category.create(:name =&gt; 'Vinyl Records')
=&gt; #&lt;Category id: 1, name: &quot;Vinyl Records&quot;, created_at: &quot;2011-08-03 15:46:11&quot;, updated_at: &quot;2011-08-03 15:46:11&quot;&gt;

?&gt; category.settings
=&gt; #&lt;ActiveConfiguration::SettingManager:0x10e7d1950 @configurable=#&lt;Category id: 1, name: &quot;Vinyl Records&quot;, created_at: &quot;2011-08-03 15:46:11&quot;, updated_at: &quot;2011-08-03 15:46:11&quot;&gt;&gt;

?&gt; category.settings[:sort]
=&gt; {:value=&gt;&quot;alphabetical&quot;, :modifier=&gt;nil}

?&gt; category.settings[:sort][:value]
=&gt; &quot;alphabetical&quot;

?&gt; category.settings[:sort][:value] = 'manual'
=&gt; &quot;manual&quot;

?&gt; category.settings[:price_filter]
=&gt; []

?&gt; category.settings[:price_filter] = [{:modifier =&gt; 'gt', :value =&gt; 10.00}, {:modifier =&gt; 'lte', :value =&gt; 25.00}]
=&gt; [{:value=&gt;10.0, :modifier=&gt;&quot;gt&quot;}, {:value=&gt;25.0, :modifier=&gt;&quot;lte&quot;}]

?&gt; category.save
=&gt; true

?&gt; category.settings[:sort][:value]
=&gt; &quot;manual&quot;

?&gt; category.settings[:price_filter]
=&gt; [{:value=&gt;10.0, :modifier=&gt;&quot;gt&quot;}, {:value=&gt;25.0, :modifier=&gt;&quot;lte&quot;}]</code></pre>
</div>
</noscript>

### Additional Notes

I accept that ActiveConfiguration's concept is a bit out there, but I've found it very useful in certain circumstances and hope that others find it useful as well. I think the [README](https://github.com/tsmango/active_configuration) provides all of the information necessary to get started using this gem, but there are additional details in the [documentation](http://rdoc.info/github/tsmango/active_configuration/master/frames).

If you have any questions or feedback, I'd love to hear from you. Please feel free to email me or [open an issue](https://github.com/tsmango/active_configuration/issues) at GitHub.