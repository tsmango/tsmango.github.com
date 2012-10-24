---
layout: post
title: "Resque in Production"
alias: /post/636319317/resque-in-production/index.html
---

Over at [Limited Pressing](http://limitedpressing.com), we're using a wonderful background queue called [Resque](http://github.com/defunkt/resque). Resque was built by [Chris Wanstrath](http://twitter.com/defunkt) of Github fame and runs on top of [Redis](http://code.google.com/p/redis/). It's fantastic and I absolutely love it. If you haven't had a chance to try it, go check it out.

When setting up Resque in production, I ran into a few issues that weren't extremely obvious to me. I ended up working through them in the end and thought I'd write something up to help others out in the future.

### Monitoring with God

Resque comes [bundled with a config file](http://github.com/defunkt/resque/blob/master/examples/god/resque.god) for [god](http://god.rubyforge.org/), but I had an issue with the way it would start up a worker. My example is only using a single worker, but you can combine my method with the bundled one. Here's how I did it:

<script src='https://gist.github.com/415369.js?file=monitor-redis-resque.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code># Redis
%w{6379}.each do |port|
  God.watch do |w|
    w.name          = &quot;redis&quot;
    w.interval      = 30.seconds
    w.start         = &quot;/etc/init.d/redis start&quot;
    w.stop          = &quot;/etc/init.d/redis stop&quot;
    w.restart       = &quot;/etc/init.d/redis restart&quot;
    w.start_grace   = 10.seconds
    w.restart_grace = 10.seconds

    w.start_if do |start|
      start.condition(:process_running) do |c|
          c.interval = 5.seconds
          c.running = false
      end
    end
  end
end

# Resque
God.watch do |w|
  w.name          = &quot;resque-1.8.0&quot;
  w.interval      = 30.seconds
  w.start         = &quot;cd /var/www/apps/limitedpressing/current &amp;&amp; rake environment RAILS_ENV=production resque:work QUEUE=high,medium,low&quot;
  w.start_grace   = 10.seconds

  # retart if memory gets too high
  w.transition(:up, :restart) do |on|
    on.condition(:memory_usage) do |c|
      c.above = 350.megabytes
      c.times = 2
    end
  end

  # determine the state on startup
  w.transition(:init, { true =&gt; :up, false =&gt; :start }) do |on|
    on.condition(:process_running) do |c|
      c.running = true
    end
  end

  # determine when process has finished starting
  w.transition([:start, :restart], :up) do |on|
    on.condition(:process_running) do |c|
      c.running = true
      c.interval = 5.seconds
    end

    # failsafe
    on.condition(:tries) do |c|
      c.times = 5
      c.transition = :start
      c.interval = 5.seconds
    end
  end

  # start if process is not running
  w.transition(:up, :start) do |on|
    on.condition(:process_running) do |c|
      c.running = false
    end
  end
end</code></pre>
</div>
</noscript>

### Restarting Workers on Deploy

The other issue I ran into was that I needed my workers to restart each time I deployed. Because Resque loads your application's environment, a new deploy with changed models, would mean that your existing Resque workers are running outdated code. The way I handled this was to QUIT my workers and let god start them back up (using the updated codebase).

<script src='https://gist.github.com/415372.js?file=deploy.rb'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>...

# Restart Passenger
deploy.task :restart, :roles =&gt; :app do
  ...

  # Restart the resque workers
  run &quot;cd #{current_path} &amp;&amp; rake queue:restart_workers RAILS_ENV=production&quot;
end</code></pre>
</div>
</noscript>

<script src='https://gist.github.com/415372.js?file=resque.rake'> </script>
<noscript>
<div class='code-snippet'>
<pre><code>namespace :queue do
  task :restart_workers =&gt; :environment do
    pids = Array.new

    Resque.workers.each do |worker|
      pids &lt;&lt; worker.to_s.split(/:/).second
    end

    if pids.size &gt; 0
      system(&quot;kill -QUIT #{pids.join(' ')}&quot;)
    end

    system(&quot;rm /var/run/god/resque-1.8.0*.pid&quot;)
  end
end</code></pre>
</div>
</noscript>

### Conclusion

At first, I wasn't really happy about quitting and restarting my workers this way, but after reviewing how Resque handled [shutting down workers](http://github.com/defunkt/resque/blob/master/lib/resque/worker.rb), it seemed like an okay approach. If you have any tips on how the environment can be reloaded without waiting for god to start new workers up, I'd like to hear them, so shoot me an email.

### Update 02/03/2011

A special thanks to Nate Daiger of [ChunkHost](http://chunkhost.com) for pointing out a much better way of collecting the worker pids in my `queue:restart_workers` rake task. Essentially, I was iterating over `Resque.workers` and calling `#worker_ids` on each worker. Nate pointed out that `#worker_ids` actually returned all pids anyway so it was unnecessary to loop. Another thing he suggested was replacing the use of `#worker_pids` with splitting the output of `#to_s`. The idea here is that `#worker_pids` uses `ps` under the hood and could erroneously pick up similarly named processes, such as resque_web.

These improvements have already been applied to the above gists. Thanks, Nate!