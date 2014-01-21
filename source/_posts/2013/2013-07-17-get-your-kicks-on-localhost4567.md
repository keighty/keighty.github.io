---
layout: post
title: "Get your kicks on route localhost:4567"
comments: true
description: ""
category: sinatra
tags: [javascript, sinatra]
---

I am building a code breaking game along the lines of Mastermind and I encountered a block.

##The problem
* I have a javascript that generates a secret code.
* I have a ruby class that compares a guess to the secret code and returns an analysis of the accuracy of the guess.
* How do I get the ruby class to process a guess and secret code from the browser?
<!--more-->
##I learned something about Ajax today.
An ajax call is built up from a few minimum parameters: a url, the HTTP method, the dataType expected, and a callback function that handles the return. You can include data from the user in the form of a data hash:

{% highlight javascript linenos%}
var the_url = "http://" + window.location.host + "/game/" + guess_string;
  var mark_string = $.ajax({
    type: "POST",
    url: the_url,
    accepts: "application/json",
    dataType: "json",
    data: { 'code' : secret_code },
    complete: function(data){
      output_mark = data['responseText'];
      process_output(secret_code, guess_string, output_mark);
    }
  });{% endhighlight %}

This code snippet is using Ajax to send the secret code and guess to the Sinatra route.

##I learned something about Sinatra today.
A route has three ingredients: an HTTP method, a URL matching pattern, and a code block.
Typically they look like this:
{% highlight ruby %}
get '/' do
  "hello world"
end{% endhighlight %}

But they can also look like this:
{% highlight ruby %}
post '/game/:guess' do
  game.guess(params[:guess])
end
private
  def game
    @game ||= Mastermind::Game.new_with_code(params[:code])
  end{% endhighlight %}

The code block returns information. That information can be anything, from a plain string (first example), or an html web page, to a JSON representation of an object (second example). What the browser does with the information it receives is the browser's business.

The overall workflow:
1. The url on my local machine looks like this: "http://localhost:4567/game/12345" (line 1). The submitted guess is 12345.
2. Ajax packages the url with the data hash containing the secret code (line 7).
3. Javascript submits the HTTP POST request to Sinatra which matches the route.
4. The route creates a Mastermind::Game object with the provided secret code.
5. The route calls the guess method on the Game object with the submitted guess.
6. The route returns a json of the method output (in this case it is a string of +/- characters indicating exact number matches(+) or matched numbers in the wrong position(-)).
7. In the ajax callback function the data from the json object is parsed and passed on for further processing.

##I learned something about routes today.
I had a mental block about routes. I thought that building the "/game/:guess" route in Sinatra meant that the user would be constantly navigating between "/mastermind" and "/game/:guess", and that the game would always be reloading with a new secret. I was thinking about routes the wrong way -- when javascript calls the route, it doesn't reload the page, it just retrieves data! I had conflated routes with navigation in my imagination, and forgot that a route is just a method call containing a request for information. Just because the information returned is often rendered as an html page doesn't mean that it has to be html. Which leads me to the penultimate point...

##I learned something about curl today.
ANYTHING can make an HTTP request. Ok, maybe that is a slight overstatement.

Browsers interpret the information retrieved using HTTP requests, but they are far from the only category of software that can. Turns out, the command-line functions just as well:
{% highlight bash linenos%}
$ curl -X POST -H "Accept: application/json" -d "code=12345" js-games.herokuapp.com/12345
+++++${% endhighlight %}

The command on line 1 will return the same string I am retrieving with $.ajax in javascript (output on line2). Sure it is a fairly plain result -- it doesn't even include a new line character, for pity's sake -- but it does the job of making an HTTP request to a route and using the result.

##I learned something about myself today.
I didn't even realize I was misunderstanding HTTP! It was one of those concepts I had already checked off my mental list of technologies to cover on my quest to become a web developer. This breakthrough has reminded me that reviewing the fundamentals in the context of learning new technologies will bring a deeper understanding of everything.

Awesome