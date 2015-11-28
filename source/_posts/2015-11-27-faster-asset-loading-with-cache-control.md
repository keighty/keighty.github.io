---
layout: post
title: "faster asset loading with Cache-Control"
date: 2015-11-27
comments: true
sharing: true
categories: [web development]
---

My side project (Virtual Playbill) is an image-heavy application, and I used YSlow to find a few quick performance wins. The first 'F' I got was for No Expires Headers:

{% img /images/151127-cache-control/add-expires-headers.png %}

### What is an Expires Header?
Loading the page requires slow and expensive network calls to download all the JavaScript, CSS, and image files. If someone visits the page more than once, I can avoid using the network by storing a local copy of the files in their browser cache -- temporary local storage that is designed for quick retrieval. I can tell the browser to cache a copy of files that don't change very often by setting Expires headers.
<!--more-->

When a web server receives a request it adds some metadata to the response in the form of HTTP headers. Headers are key-value pairs that tell the browser information about the file it has received, such as how big it is (`Content-Length`), what kind it is (`Content-Type`), and how long the browser should cache the response (`Cache-Control`).

In the case of Virtual Playbill, all the images are served from AWS Simple Storage Service (S3), and there are a couple of ways to add headers.

### 1. Tedious Manual Addition
You can add headers manually to a single asset stored in S3 using the metadata section:
{% img /images/151127-cache-control/add-metadata-in-s3-0.png %}
{% img /images/151127-cache-control/add-metadata-in-s3-1.png %}
{% img /images/151127-cache-control/add-metadata-in-s3-2.png %}

But I have more than a hundred files to change (yawn).

### 2. Write a Nodejs Script using `aws-sdk`

```javascript
var aws = require('aws-sdk')
var s3 = new aws.S3();

var params = {
  Bucket: 'Your_bucket_name',
}

new Promise(function (resolve, reject) {
  s3.listObjects(params, function(err, data) {
    if (err) console.log('Something went wrong when retrieving the list of objects: ' + err)
    resolve(data.Contents.map(function (image) { return image.Key }))
  })
}).then(function (imageList) {
  return collectImageParams(imageList)
}).then(function (imageChangeParams) {
  imageChangeParams.forEach(function (params) {
    s3.copyObject(params, function (err) {
      if (err) console.log("Oops! Something went wrong with copy/replace for " + params)
    })
  })
})

// Duplicating a file in S3 will change the metadata.
// You can make sure the new file has the same
// access control by passing through the right permissions
// and ContentType along with the new CacheControl header
function collectImageParams(imageList) {
  return imageList.map(function (imageName) {
    var imageParams = {
      'Bucket': params.Bucket,
      'ACL': 'public-read',
      'MetadataDirective': 'REPLACE',
      'CacheControl': 'max-age=2592000',
      'ContentType': 'image/jpeg'
    }
    imageParams.Key = imageName
    imageParams.CopySource = '/' + imageParams.Bucket +'/' + imageParams.Key
    return imageParams
  })
}
```
Still.. I don't want to have to run this script every time I upload a new file (yawn)...

### 3. Add Headers Every Time You Upload

```
S3Upload.prototype.uploadToS3 = function(file, url, public_url) {
  var this_s3upload, xhr;
  this_s3upload = this;
  xhr = this.createCORSRequest('PUT', url);
  ...
  xhr.setRequestHeader('Content-Type', file.type);
  xhr.setRequestHeader('x-amz-acl', 'public-read');

  // Set the Cache-Control header for every file upload
  xhr.setRequestHeader('Cache-Control', 'max-age=2592000');
  return xhr.send(file);
};
```

### Great Results
No matter how you add the Cache-Control header, the result is amazing:
{% img /images/151127-cache-control/cache-control.png %}

### Resources
For more information about HTTP headers and the browser cache, check out these useful resources:

* [Beginners Guide to HTTP Cache Headers](http://www.mobify.com/blog/beginners-guide-to-http-cache-headers/)
* [Optimizing Content Efficiency Using HTTP Caching](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=en)