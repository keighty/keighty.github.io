---
layout: post
title: "preflight check with CORS -- chainPreflight option"
date: 2016-04-09
sharing: true
categories: [java, CORS, browsers]
---


I am working through setting up CORS on a web server, and [learning all about Cross-Origin Resource Sharing](http://katieleonard.ca/blog/2016/preflight-check-with-cors/). When I came across the **chainPreflight** option, I also learned that I didn't have a strong enough understanding of web server vocabulary.<!--more--> Here are the docs for the standard Jetty CrossOriginFilter object:

{% img /images/160329-preflight-cors/chainPreflight-docs.png %}

Most references and docs for CORS filtering use the exact same language to describe `chainPreflight`, but what does it mean for requests to be "chained to their target resource?" Is this a good thing? If it defaults to true, it must be the expected behaviour... What about "otherwise the filter will response to the preflight?" Is that even proper grammar? I asked on [StackOverflow](http://stackoverflow.com/q/36414029/1279340), and five days later, still no answers.

Luckily, I have access to an army of experts at work, and this is what I learned about **chainPreflight**:

{% img /images/160329-preflight-cors/chainPreflight-seq.png %}

The 'target resource' is the server endpoint that the request is targeting. If the preflight request is chained to the target resource, it it will pass through the filter (which adds the necessary `Allows-*` headers) all the way to the endpoint, and the endpoint must have some business logic for handling and responding to the request.

If the preflight request is NOT chained to the target resource, then the filter will add the necessary `Allows-*` headers and send a response back directly.

Since I don't want to do anything with the OPTIONS preflight request, I can confidently set this option to false, saying "whatever you've got, send it over!"

Awesome.
