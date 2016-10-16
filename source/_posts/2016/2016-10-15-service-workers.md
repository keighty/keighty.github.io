---
layout: post
title: "service workers"
date: 2016-10-15
sharing: true
categories: [javascript]
---

A service worker is a script that sits between a web page and the network, acting as a proxy for network requests: if the network is available, the request is passed on to the target url; if the network is not available, the service worker will handle the request itself by checking cached responses or or queuing the request for synchronization once the network is available. Service workers are bridging the gap between native mobile applications and traditional web applications that require a network connection to function. Sometimes called progressive web apps, these applications enable a seamless offline experience when the user is not connected to a network.

<!--more-->
Service workers can be used for data synchronization in the background, but data fetching and sync-ing are only one of the ways we can leverage this API. We can:

* use background resources to perform client-side compilation of assets  (useful for web development)
* pre-fetch data that the user is likely going to need in the near future (think photo albums or playlists)
* share large data sets between multiple pages
* notify mobile users of updates via push notifications

For example, [The Washington Post](https://www.washingtonpost.com/) uses service workers to send push notifications of breaking news content to users of their web application on android.

## How do they work?
Service workers rely on the [Promise API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) -- a Promise is a proxy for "a value that may be  available now, or in the future, or never." They also use the event emitter pattern, where listeners are registered to handle specific events, such as `fetch` requests.

To employ a service worker you must first register a script with the service worker API: `navigator.serviceWorker.register('./sw.js')`. Checkout the following example ([get the gist](https://gist.github.com/keighty/ec1a37a0f6475d7cdf2db64b01eff445.js)):

```html
<!-- index.html references the javascript application -->
<html>
  <body>
    <h1>Service worker test app</h1>
    <script src="./app.js"></script>
  </body>
</html>
```
```javascript
// app.js
.register('sw.js') // register a worker script with the service
navigator.serviceWorker
  .then(function(reg) {
    // do the following if registration is successful
    if(reg.installing) {
      // first visit or fresh reload of the worker
      console.log('Service worker installing on registration.');
    } else if(reg.waiting) {
      // a worker may be installed but waiting for another worker to stop
      console.log('Service worker already installed on registration.');
    } else if(reg.active) {
      // pages from the same origin may share a service worker
      console.log('Service worker already active on registration.');
    }
  }).catch(function(error) {
    // if registration is unsuccessful, tell us why
    console.log('Registration failed with ' + error);
  });
```
```javascript
// sw.js
console.log('inside registration script')

this.addEventListener('install', function(event) {
  console.log('Service worker installed.')
});

this.addEventListener('activate', function (event) {
  console.log('Service worker activated.')
})
```

Service workers can be registered with a specific origin and path to watch. Since they run in a background thread with a context separate from the application javascript, service workers have no DOM access. So don't try to perform synchronous work (like accessing LocalStorage) or update the DOM from a service worker.

Running this example code sends some output to the console:
{% img /images/161015-service-workers/service-worker-demo1.png %}

#### NOTE: A single service worker can control many pages (so be careful with global variables).

{% img /images/161015-service-workers/service-worker-demo2.png %}

## Developing with service workers
Service workers can only function in a secure context, which means that you can only register a service worker from a web application that is served over `https`. One option is to use [GitHub pages](https://pages.github.com/) to deploy your application, as these pages are generally served over `https`. Another option is to use a local web server (`localhost` is considered semi-secure).

I use [Browser-sync](https://www.browsersync.io/), a super light-weight easy to start web server. In my project directory, I start the browser-sync server and set it to watch my javascript files:

```bash
$ npm install -g browser-sync
$ browser-sync start --server --files *.js
[BS] Access URLs:
 ----------------------------------
       Local: http://localhost:3000
    External: http://10.0.0.8:3000
 ----------------------------------
          UI: http://localhost:3001
 UI External: http://10.0.0.8:3001
 ----------------------------------
[BS] Serving files from: ./
[BS] Watching files...
```

Browser-sync is so helpful, it will even open a browser window for you. :awthanks:

Don't forget to checkout the Chrome developer tools, which can be super helpful in creating different states for your service worker

{% img /images/161015-service-workers/service-worker-dev-tools.png %}

------------------------------
## MOAR Resources
For more about the service worker API, checkout these resources:

* the [W3C spec](https://www.w3.org/TR/service-workers/)
* the [MDN docs](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)
* and the [example](https://github.com/mdn/sw-test/) https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers
