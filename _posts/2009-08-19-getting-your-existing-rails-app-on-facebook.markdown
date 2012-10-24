---
layout: post
title: "Getting Your Existing Rails App on Facebook"
alias: /post/166959450/getting-your-existing-rails-app-on-facebook/index.html
---

There's a ton of information already out there about writing a new Facebook app with Ruby on Rails. You use [facebooker](http://github.com/mmangino/facebooker), you call a method at the top of your `application_controller` and you're done. It's pretty easy. The problem is that when you have an existing Rails app and you want to extend it to support alternate fbml views for a Facebook application, things are a bit trickier.

Here's how I did it:

You'll have to start with the usual setup:

* Create a Facebook app from the [Developer Application](http://www.facebook.com/developers)
* Install the [plugin](http://github.com/mmangino/facebooker)
* Setup your ssh tunnel for development
* Setup your `config/facebook.yml`

I won't bother explaining how to do all of this stuff. There are [tons](http://www.pragprog.com/titles/mmfacer/developing-facebook-platform-applications-with-rails) of [resources](http://www.pragprog.com/screencasts/v-mmfacer/rails-development-for-the-facebook-platform) out [there](http://peepcode.com/products/rails-on-facebook).

After you get that done, it's time to start serving authenticated fbml views for requests coming from Facebook. In the facebooker world, you need to call `ensure_authenticated_to_facebook`. Unfortunately, if you do that in your `application_controller`, it will try and do a Facebook authentication for every request coming into your application, not just the ones actually coming from Facebook. Since you are trying to extend your existing application with Facebook views, this isn't what you want.

It turns out that facebooker does two very important things for you by just having the plugin configured in your application. The first is that it forces the format of all of the Facebook requests to be fbml. Next, it adds an object to `session[:facebook_session]`. It's safe to say that if your request has a format of fbml and there is something in `session[:facebook_session]`, you've got yourself a Facebook request.

To catch and authenticate just the requests coming from Facebook, you'll want to add the next bit of code to your `application_controller`:

<script src='https://gist.github.com/1165276.js?file='> </script>
<noscript>
<div class='code-snippet'>
<pre><code>before_filter :require_login_for_facebook

def require_login_for_facebook
  if params[:format] == 'fbml'
    ensure_authenticated_to_facebook

    if !session[:facebook_session].nil?
      # we'll do something with users
      # here in the next post about fb
    end
  end
end</code></pre>
</div>
</noscript>

Now, let's say you have a `movies_controller` that uses an `app/views/layouts/movies.html.erb` layout. You'll want to add an `app/views/layouts/movies.fbml.erb` layout for requests coming from Facebook. Next, you want a Facebook specific view for your `index` action. You already have an `app/views/movies/index.html.erb` right? Well, just add an `app/views/movies/index.fbml.erb`.

That's it, you're done! Now, when a request comes into your regular application for `/movies`, the regular `index.html.erb` view will be rendered inside of your regular `movies.html.erb` template. But, when a request comes in from your Facebook application to `/fb-app-name/movies`, it's authenticated with facebooker, the facebook user will have to have added your application and your new `index.fbml.erb` view will automatically be rendered inside of your `movies.fbml.erb` layout.

I will post another article soon talking about how to approach integrating your existing user base with a Facebook user base.

*Like this article? Be sure to read the [second article](http://thomasmango.com/2009/08/20/making-your-existing-user-base-play-nice-with-your) in this series titled: "Making Your Existing User Base Play Nice With Your Facebook User Base".*