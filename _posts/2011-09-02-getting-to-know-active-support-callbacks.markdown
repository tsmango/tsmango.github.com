---
layout: post
title: "Getting to Know ActiveSupport::Callbacks"
alias: /2011/09/01/getting-to-know-active-support-callbacks/index.html
---

While writing [ActiveConfiguration](https://github.com/tsmango/active_configuration), I needed a way to run a callback when certain methods were executed. Thankfully, ActiveSupport was already available, so I had access to [ActiveSupport::Callbacks](http://api.rubyonrails.org/classes/ActiveSupport/Callbacks.html).

### What is a Callback?

At this point, you may be wondering what a callback even is. Simply put, a callback allows you to run some code (usually a method) automatically when another piece of code is run. In Rails, you'll commonly see callbacks that run before, after or even around other bits of code. In fact, if you've ever used `before_create`, `after_save` or any of the other ActiveRecord [life cycle callbacks](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html), you've already been using the ActiveSupport::Callbacks module, albeit indirectly and through a layer of abstraction.

While those life cycle callbacks are indispensable in an ActiveRecord model, they wouldn't help me in ActiveConfiguration. So, what if like me you want a custom callback or need callbacks in a class that isn't an ActiveRecord model?

### Walking Through ActiveConfiguration::Option

Let's start by looking at a simple class with three methods:

<script src='https://gist.github.com/1187700.js?file=option-0.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>class Option
  def default(value)
    @default_value = (value.is_a?(Symbol) ? value.to_s : value)
  end

  def format(value)
    @allowed_format = (value.is_a?(Symbol) ? value.to_s : value)
  end

  def validate!
    ...
  end
end</code></pre>
</div>
</noscript>

Here, the plan is to run the `#validate!` method after the `#default` and `#format` methods are run. To do that, let's first include ActiveSupport::Callbacks:

<script src='https://gist.github.com/1187700.js?file=option-1.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>class Option
  include ActiveSupport::Callbacks

  ...
end</code></pre>
</div>
</noscript>

Next, we'll define a callback named `validate` (not to be confused with the `#validate!` method defined above - more on this later):

<script src='https://gist.github.com/1187700.js?file=option-2.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>class Option
  include ActiveSupport::Callbacks

  define_callbacks :validate

  ...
end</code></pre>
</div>
</noscript>

After defining a new callback named `validate`, we'll use `set_callback` to actually set a callback up to run:

<script src='https://gist.github.com/1187700.js?file=option-3.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>class Option
  include ActiveSupport::Callbacks

  define_callbacks :validate

  set_callback :validate, :after, :validate!

  ...
end</code></pre>
</div>
</noscript>

When using ActiveSupport::Callbacks, I think `#set_callback` may be a source of confusion for some people. Unfortunately, by showing a complicated example with a subclass in addition to using the same name for both the callback itself as well as the method to be run, the [documentation](http://api.rubyonrails.org/classes/ActiveSupport/Callbacks.html) doesn't help to make it any easier.

Admittedly, my validate example isn't much better. Here, the callback is named `validate` and the method that I want to be run when the callback fires is `validate!` - a subtle, but important distinction.

So, what exactly are we passing to `#set_callback`?

1. The *name* of the callback we used in `define_callbacks`.
2. *When* the method should be run in relation to the callback being defined <sup id="fnr-1"><a href="#fn-1">1</a></sup>.
3. The *method* to execute when the callback fires.

The way I remember what `set_callback` means is by reading it backwards:

> "*Execute the `validate!` method `after` the `validate` callback is fired.*"

### Putting It All Together

Up to this point, we've only defined what should happen *when* the `validate` callback fires. We've yet to specify when the `validate` callback actually *should* be fired.

To do that, we'll use the `#run_callbacks` method:

<script src='https://gist.github.com/1187700.js?file=option.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>class Option
  include ActiveSupport::Callbacks

  define_callbacks :validate

  set_callback :validate, :after, :validate!

  def default(value)
    run_callbacks :validate do
      @default_value = (value.is_a?(Symbol) ? value.to_s : value)
    end
  end

  def format(value)
    run_callbacks :validate do
      @allowed_format = (value.is_a?(Symbol) ? value.to_s : value)
    end
  end

  ...

  def validate!
    ...
  end
end</code></pre>
</div>
</noscript>

(For the unabridged version of ActiveConfiguration::Option, see the [source on GitHub](https://github.com/tsmango/active_configuration/blob/v1.0.0/lib/active_configuration/option.rb).)

That's really all there is to it when looking to add custom callbacks to your own classes. By wrapping your code in `#run_callbacks`, any callbacks you've setup to run `before`, `after` or even `around`, will be executed at the correct time.

### Life Cycle Callbacks in ActiveRecord

Fair warning, things start to get a bit more hairy from here on out, so if you're just interested in using callbacks in your own class, feel free to skip the rest of this article. But if you'd like to dive into Rails a bit, keep reading.

As I mentioned earlier, ActiveRecord supports a number of [life cycle callbacks](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html) that are extremely useful. These callbacks are actually defined using ActiveSupport::Callbacks under the hood and after looking at the example above, we should be able to figure out how that's being done.

First, let's look at the [ActiveRecord::Callbacks module](https://github.com/rails/rails/blob/v3.1.0/activerecord/lib/active_record/callbacks.rb):

<script src='https://gist.github.com/1187700.js?file=active-record-callbacks.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>require 'active_support/core_ext/array/wrap'

module ActiveRecord

  module Callbacks
    extend ActiveSupport::Concern

    CALLBACKS = [
      :after_initialize, :after_find, :after_touch, :before_validation, :after_validation,
      :before_save, :around_save, :after_save, :before_create, :around_create,
      :after_create, :before_update, :around_update, :after_update,
      :before_destroy, :around_destroy, :after_destroy, :after_commit, :after_rollback
    ]

    included do
      extend ActiveModel::Callbacks
      include ActiveModel::Validations::Callbacks

      define_model_callbacks :initialize, :find, :touch, :only =&gt; :after
      define_model_callbacks :save, :create, :update, :destroy
    end

    def destroy #:nodoc:
      run_callbacks(:destroy) { super }
    end

    def touch(*) #:nodoc:
      run_callbacks(:touch) { super }
    end

  private

    def create_or_update #:nodoc:
      run_callbacks(:save) { super }
    end

    def create #:nodoc:
      run_callbacks(:create) { super }
    end

    def update(*) #:nodoc:
      run_callbacks(:update) { super }
    end
  end
end</code></pre>
</div>
</noscript>

Some of this should be familiar to you. First you'll notice that `ActiveModel::Callbacks` is being extended, so we'll want to look at that in a moment. Next, you'll see two calls to `define_model_callbacks` and multiple mentions of `run_callbacks`, which we used earlier in our `#default` and `#format` methods.

Let's pick out just one of the methods above to focus on, say `create_or_update`:

<script src='https://gist.github.com/1187700.js?file=active-record-callbacks-0.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>def create_or_update
  run_callbacks(:save) { super }
end</code></pre>
</div>
</noscript>

This method is overriding AR's `#create_or_update` method and wrapping a call to the existing method (`super`) with `run_callbacks(:save)`. From what we learned earlier, we know this means that any callbacks to `save` should be fired here.

But, this doesn't tell the entire story. We haven't seen a call to `set_callback` yet which we know is necessary to actually hook a method up to a callback.

To get to the bottom of this implementation, let's venture into [ActiveModel::Callbacks](https://github.com/rails/rails/blob/v3.1.0/activemodel/lib/active_model/callbacks.rb):

<script src='https://gist.github.com/1187700.js?file=active-model-callbacks.rb'> </script>

I admit, this may look a little scary, but let's go through it a piece at a time.

First, our faithful ActiveSupport::Callbacks is included:

<script src='https://gist.github.com/1187700.js?file=active-model-callbacks-0.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>def self.extended(base)
  base.class_eval do
    include ActiveSupport::Callbacks
  end
end</code></pre>
</div>
</noscript>

Next, we see `define_model_callbacks`, which was called in ActiveRecord::Callbacks:

<script src='https://gist.github.com/1187897.js?file=active-model-callbacks-1.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>def define_model_callbacks(*callbacks)
  options = callbacks.extract_options!
  options = {
     :terminator =&gt; &quot;result == false&quot;,
     :scope =&gt; [:kind, :name],
     :only =&gt; [:before, :around, :after]
  }.merge(options)

  types   = Array.wrap(options.delete(:only))

  callbacks.each do |callback|
    define_callbacks(callback, options)

    types.each do |type|
      send(&quot;_define_#{type}_model_callback&quot;, self, callback)
    end
  end
end</code></pre>
</div>
</noscript>

If you've ever gone spelunking in the Rails source before, the beginning of that method where options are being extracted should look familiar, otherwise, let's not worry about it.

The more relevant part to this discuss is when the array of callbacks is iterated over. For each callback, `define_callbacks` (the method we used earlier) is called. This means callbacks are defined from those specified back in ActiveRecord::Callbacks such as `:initialize`, `:find`, `:touch` and `:save`, `:create`, `:update`, `:destroy`.

Additionally, after specifying each callback, the allowed types (`before`, `after`, `around`) are iterated over and the actual callback methods are defined.

Let's just take one example. One of the callbacks defined was `save`. For this callback, `send("_define_#{type}_model_callback", self, callback)` would be called three times (once for each of `before`, `after` and `around`). Looking further down the implementation, we'll see the three methods being called:

<script src='https://gist.github.com/1187897.js?file=active-model-callbacks-2.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>def _define_before_model_callback(klass, callback) #:nodoc:
  klass.class_eval &lt;&lt;-CALLBACK, __FILE__, __LINE__ + 1
    def self.before_#{callback}(*args, &amp;block)
      set_callback(:#{callback}, :before, *args, &amp;block)
    end
  CALLBACK
end

def _define_around_model_callback(klass, callback) #:nodoc:
  klass.class_eval &lt;&lt;-CALLBACK, __FILE__, __LINE__ + 1
    def self.around_#{callback}(*args, &amp;block)
      set_callback(:#{callback}, :around, *args, &amp;block)
    end
  CALLBACK
end

def _define_after_model_callback(klass, callback) #:nodoc:
  klass.class_eval &lt;&lt;-CALLBACK, __FILE__, __LINE__ + 1
    def self.after_#{callback}(*args, &amp;block)
      options = args.extract_options!
      options[:prepend] = true
      options[:if] = Array.wrap(options[:if]) &lt;&lt; &quot;!halted &amp;&amp; value != false&quot;
      set_callback(:#{callback}, :after, *(args &lt;&lt; options), &amp;block)
    end
  CALLBACK
end</code></pre>
</div>
</noscript>

If you look at the first method, `_define_before_model_callback`, you'll see that a class method is being dynamically defined for the callback given (`save`). So, this call to `_define_before_model_callback` will define the method `before_save` and make it available for you to use in your ActiveRecord model, like so:

<script src='https://gist.github.com/1187897.js?file=post.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>class Post &lt; ActiveRecord::Base
  before_save :generate_slug

  def generate_slug
    self.title = self.title.mb_chars.normalize(:kd).gsub(/[^\x00-\x7F]/n, '').to_s.gsub(/[']+/, '').gsub(/\W+/, ' ').strip.downcase.gsub!(' ', '-')
  end
end</code></pre>
</div>
</noscript>

Now, if you look again at the the `before_save` method that was defined from within `_define_before_model_callback`, you'll see a call to `set_callback`. This means that a method you pass to `before_save` will be passed into the `set_callback` method and will be executed `before` the `save` callback.

Looking back to ActiveRecord::Callback, you'll remember the following method:

<script src='https://gist.github.com/1187700.js?file=active-record-callbacks-0.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>def create_or_update
  run_callbacks(:save) { super }
end</code></pre>
</div>
</noscript>

This means that the method passed to `before_save` in the `Post` class above is set as a callback method to the `save` callback and before the `#create_or_update` method is run, the callback method you set will be executed.

### Final Thoughts

When used thoughtfully, ActiveSupport::Callbacks can be very helpful and I think this example of how Rails uses the underlying module to expose specific life cycle callback methods to ActiveRecord models is fascinating. And really, this module is just one of the many excellent things you'll find in the Rails codebase. So while it may feel overwhelming to try and understand the entire Rails source all at once, taking an example like this where you look at a specific method and peel back the layers of how it's implemented can be very approachable and can lead to understanding and reusing some great code that's already available to you.

If you have any feedback, please feel free to send me an email. I'd love to hear from you.

---

<ol>
<li id="fn-1">This can either be <code>before</code>, <code>after</code> or <code>around</code>. <a href="#fnr-1">&#8617;</a></li>
</ol>