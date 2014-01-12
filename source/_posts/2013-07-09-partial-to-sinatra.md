---
layout: post
title: "Partial to Sinatra"
description: ""
category: sinatra
tags: []
---

Having just discovered the magic of using partials to refactor html in rails, imagine my delight when I found it to be even easier in Sinatra.

A partial is a just a fragment of html (or erb). They can be used for rendering duplicate or dynamic content into otherwise static pages.

I was writing html for my JSGames sinatra application when I caught myself copying a snippet of html onto my clipboard. Why on earth would I duplicate code? I want to include a block of text on both the index and the game page, but I don't want to have to modify it in two places when I inevitably get around to rewriting it.

The solution?

Factor out the duplicate html into a partial (begin name with _ for identification purposes). For example `_gameDescription.erb`
{% highlight html %}
<h1>Game Title</h1>
<p>Game description</p>{% endhighlight %}

Add that partial wherever you want to display this snippet of code:
{% highlight ruby %}
<%= erb :_gameDescription  %>{% endhighlight %}

For example:
{% highlight ruby %}
<div class="span4 pagination-centered">
  <%= erb :_solitaireDesc  %>
  <a href="/game" class="btn btn-large">Play</a>
</div>{% endhighlight %}

Check out the final product on <a href="http://js-games.herokuapp.com/solitaire">JSGames</a>.

Awesome