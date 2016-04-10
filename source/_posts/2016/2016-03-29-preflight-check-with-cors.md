---
layout: post
title: "preflight check with CORS"
date: 2016-03-29
sharing: true
categories: [javascript, browsers]
---

Modern web applications can draw resources from anywhere on the web. Fonts, JavaScript libraries, images, and other data can be fetched from CDNs, Amazon, IMDB, or anywhere else that provides a public API. Early browsers restricted web applications to same-origin requests, which prevented the sharing of resources between applications, but also ensured that data from one application could not be tampered with by another. The thinkers at the W3C came up with a means of communicating safe cross-origin requests that would allow even destructive remote actions to be performed, as long as the server consented to receive the request. Enter CORS, and preflighting.

<!--more-->

### What is CORS?

Cross-Origin Resource Sharing occurs when JavaScript on a web page requests data from (or sends data to) a location on a different host. Any website that embeds a video from YouTube, uses a custom font, or posts to social media on your behalf, is making a CORS request. CORS is a [set of web standards](https://www.w3.org/TR/cors/) developed to enable safe cross-domain communication.

### What is preflighting?

CORS functions through the specification of new HTTP headers that allow servers to describe the origin and nature of the request. For each outgoing request to a different domain, the browser will look at the headers to determine if the request that looks like it will affect data on the receiving server. Any simple request that uses one of the common HTTP verbs and a basic Content-Type are allowed to pass without further comment. But if the request has different header values, the browser checks with the server first to make sure the request is expected. This is called a pre-flight check.

### What does a simple CORS request look like?

For simple requests (no pre-flight check), the method must be one of:
    - GET
    - HEAD
    - POST

The only extra headers allowed are:
    - Accept
    - Accept-Language
    - Content-Language
    - Content-Type

For the Content-Type header, the only allowed values are:
    - application/x-www-form-urlencoded
    - multipart/form-data
    - text/plain

XMLHttpRequests (aka XHRs or ajax) use CORS to mitigate the risks of pulling or pushing sensitive data across domains. For example:

```javascript
var body = 'very safe, ordinary text content'
var url = 'http://api.example.com'
var request = new XMLHttpRequest()
request.open('POST', url, true)
request.setRequestHeader('Content-Type', 'text/plain')
request.send()
```

{% img /images/160329-preflight-cors/cors-without-preflight.png %}

The browser checks out the request, sees that it is unlikely to have any negative consequences, and passes it along to the requested endpoint. In the Network tab, simple CORS requests will appear by themselves:

{% img /images/160329-preflight-cors/one-request.png %}

### What does a pre-flight CORS request look like?

For non-simple requests, the method could be any of the HTTP verbs along with any other combination of headers. For example:

```javascript
var body = 'possibly unstable or malicious content'
var url = 'http://api.example.com'
var request = new XMLHttpRequest()
request.open('POST', url, true)
request.setRequestHeader('Content-Type', 'application/json')
request.send()
```

In this request I am trying to POST a JSON object to an API endpoint, which could potentially have destructive consequences for data on the other side of the endpoint. When the browser inspects the headers of this request it gets suspicious, and fires off a preliminary request to the endpoint with the meta data from the request:

{% img /images/160329-preflight-cors/cors-with-preflight.png %}

The server at the endpoint responds to the pre-flight request with a list of authorized headers, including acceptable request origins, content, and actions.

Ignoring for a moment that the requests fail (because api.example.com does not exist), the non-simple CORS request produces two requests instead of one:
{% img /images/160329-preflight-cors/two-requests.png %}

The result of the pre-flight OPTIONS request will determine if the second is ever sent on to the endpoint. This is how browsers keep your application data secure from malicious or unintended changes.

### Why OPTIONS, and not POST?
I found [this great answer](http://stackoverflow.com/a/16945321) on stackoverflow that explains why the creators of CORS made a new request type, `OPTIONS`. Before the CORS standards were introduced, neither browsers nor servers knew how to handle cross-domain requests. Browsers would throw an error, but servers would process the request without concern. Inventing a new method type that CORS-enabled browsers AND CORS-aware servers could handshake with ensures that both sides are sensible to the meaning of the pre-flight request.

#### RESOURCES

* Learn more about the web standards at [W3C](https://www.w3.org/TR/cors/)
* Find out more about how CORS is [implemented at Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS#Simple_requests:)
* Find out what web browsers support CORS at [caniuse.com](http://caniuse.com/#search=CORS)
