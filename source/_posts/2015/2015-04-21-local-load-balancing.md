---
layout: post
title: "local load balancing"
date: 2015-04-22
comments: true
sharing: true
categories: [backend, rails, nginx]
---

You can mimic the end-user's UI experience accurately enough by running application code locally, but what about the backend? Once your code is deployed to production, requests will be divided between dozens of servers. If there is variation in the code paths running on each server (like during a feature rollout, for example), it is useful to determine beforehand if there are any dangerous conflicts. Enter local load-balancing.
<!--more-->

[NGiNX](http://nginx.org/en/) is load balancing software that distributes requests across multiple servers, and it's easy to get running quickly.

### Installation
You can download and compile from source at [NGiNX.org](http://nginx.org/en/download.html), or use `brew install nginx` on MacOS-X.

{% codeblock lang:bash %}
➜  api_playground  brew install nginx
...

The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that nginx can run without sudo.

To have launchd start nginx at login:
    ln -sfv /usr/local/opt/nginx/*.plist ~/Library/LaunchAgents
Then to load nginx now:
    launchctl load ~/Library/LaunchAgents/homebrew.mxcl.nginx.plist
Or, if you don't want/need launchctl, you can just run:
    nginx
{% endcodeblock %}

Start the server with the command `nginx`.

### Configuration
Your NGiNX installation added a config file: `/usr/local/etc/nginx/nginx.conf`. It is mostly commented configuration examples, so you can simply replace the current `server` configuration block with this:

{% codeblock lang:text /usr/local/etc/nginx/nginx.conf %}
upstream myapp1 {
  server localhost:3000;
  server localhost:3030; #add as many servers as you want here
}

server {
  listen 8080;
  server_name localhost;
  location / {
    proxy_pass http://myapp1;
  }
}
{% endcodeblock %}

Reload your NGiNX server with `nginx -s reload`, and hit [localhost:8080](localhost:8080) to ensure you have configured it correctly.

{% img /images/150421-local-loadbalancing/welcome_nginx.png %}

### Start your servers
NGiNX is configured and listening on ports 3000 and 3030. Now we just have to connect some servers!

**Server 1**: `bundle exec rails server -p 3030`

**Server 2**: `bundle exec rails server -p 3000 --pid tmp/pids/server2.pid`

> Pro-tip: The Rails server stores its process id in a temporary file (`/tmp/pids/server.pid`), and will complain if you start another server unless you specify a new temporary pid file.

Check your progress by hitting localhost:8080:

{% img /images/150421-local-loadbalancing/load_balancing.png %}

Checkout the [docs](http://nginx.org/en/docs/http/load_balancing.html) for more examples of nginx configuration.
