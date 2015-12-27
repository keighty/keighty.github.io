---
layout: post
title: "uploading images from node and angular to s3"
date: 2015-02-24
comments: true
sharing: true
categories: [angular, aws, node]
---

Amazon Web Services allow you to do everything, so it is hard to figure out how to do anything. I have a node/angular/mongo stack running on Heroku, and want to use Amazon to store images. I was delighted to stumble across [*Direct to S3 Uploads in Node.js*](https://devcenter.heroku.com/articles/s3-upload-node), written by Will Webberly for the Heroku Dev Center blog.
Following his [example](https://github.com/flyingsparx/NodeDirectUploader), I was able to upload images directly from the browser to S3, saving my app server a whole lot of work.
<!--more-->

In the example, when a user selects a file for upload, the browser asks the node server for a temporary signed request. The server replies with a signed url, and the browser can send the data directly to Amazon. TADA -- Almost. With a few exceptions. One of the prerequisites is that you know how to set up an S3 bucket and IAM user with the correct access controls.

#### Setting the S3 stage
When S3 receives a request it must verify that the requester has the proper permissions -- both at the account level, as well as at the bucket level. With a seemingly infinite number of services available through Amazon, I found that I had to aggregate information from a few different sources.

1. Create a bucket
    * See [this Amazon tutorial](http://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) for how to create a bucket.
2. Create a user
    * See [this Amazon tutorial](http://docs.aws.amazon.com/IAM/latest/UserGuide/Using_SettingUpUser.html#Using_CreateUser_console) for creating an IAM User.
    * Be sure to record the generated `Access Key ID` and `Secret Access Key`. They will act as user name and password for accessing your bucket.
3. Grant the IAM User access to S3
    * In the IAM User section of the AWS Management Console, select `Attach Policy`, and add `AmazonS3FullAccess` from the list
    {% img /images/IAM_Management_Console.png %}
4. Grant the IAM User access to the bucket
    * Use [this Amazon tool](http://awspolicygen.s3.amazonaws.com/policygen.html) to help you generate an Access Control Policy. The key pieces you need for the policy generator are the AWS Principle (the user you created), which you enter in the format `arn:aws:iam::<your_account_number>:<IAM_user_name>`, and the Resource (the bucket you created), which you enter in the format `arn:aws:s3:::<your_bucket_name>/*`.

{% codeblock Your access control policy should look like this example: %}
{
  "Version":"2012-10-17",
  "Id": "Policy1234567890123",
  "Statement":[{
    "Sid":"Stmt123456789",
    "Effect":"Allow",
    "Principal": {
            "AWS": "arn:aws:iam::111122223333:specialUser"
    },
    "Action":[
      "s3:PutObject",
      "s3:DeleteObject",
      "s3:GetObject"
    ],
    "Resource":"arn:aws:s3:::examplebucket/*"
  }]
}
{% endcodeblock %}

In case you get lost, here is [one more invaluable Amazon tutorial](http://docs.aws.amazon.com/AmazonS3/latest/dev/how-s3-evaluates-access-control.html).

#### Angular client side
Using an angular app on the client side is also a slight deviation from the Webberly tutorial. Angular [does not support](https://github.com/angular/angular.js/issues/1375) an `ng-change` binding on file input elements, but there is a [workaround](http://stackoverflow.com/a/17923521):

{% codeblock lang:html %}
<input type="file" class="form-control btn" id="image" onchange="angular.element(this).scope().s3Upload(this)">
{% endcodeblock %}

Additionally, instead of adding the upload function as plain javascript in your html template, add the `s3Upload` function onto the `$scope` in your controller.

{% codeblock lang:javascript %}
$scope.s3Upload = function(){
  var status_elem = document.getElementById("status");
  var url_elem = document.getElementById("image_url");
  var preview_elem = document.getElementById("preview");
  var s3upload = new S3Upload({
      s3_object_name: showTitleUrl(),  // upload object with a custom name
      file_dom_selector: 'image',
      s3_sign_put_url: '/sign_s3',
      onProgress: function(percent, message) {
          status_elem.innerHTML = 'Upload progress: ' + percent + '% ' + message;
      },
      onFinishS3Put: function(public_url) {
          status_elem.innerHTML = 'Upload completed. Uploaded to: '+ public_url;
          url_elem.value = public_url;
          preview_elem.innerHTML = '<img src="'+ public_url +'" style="width:300px;" />';
      },
      onError: function(status) {
          status_elem.innerHTML = 'Upload error: ' + status;
      }
  });
};
{% endcodeblock %}

Note the diff on line 6 -- taking a peek into the [S3Upload source](https://github.com/flyingsparx/NodeDirectUploader/blob/master/public/javascripts/s3upload.js#L23-L29), you can set a custom file name by passing a `s3_object_name` option. Otherwise, every object will be named `default_name`.

{% codeblock lang:javascript %}
$scope.s3_upload = function(){
  ...
  var s3upload = new S3Upload({
    s3_object_name: showTitleUrl(),  // upload object with a custom name
    file_dom_selector: 'image',
    s3_sign_put_url: '/sign_s3',
  ...
}

function showTitleUrl() {
  var title = $scope.show.title.split(' ').join('_');
  var dateId = Date.now().toString();
  return dateId + title;
}
{% endcodeblock %}

#### Add `image` to your schema
The last stumbling block I encountered was saving the image url into mongo. The image would upload and appear just fine, but wouldn't persist the url as a property of the object. I had forgotten to add the image to my mongo Schema -- the schema acts a bit like a whitelist for saving attributes through mongoose into mongo. Adding the [imageUrl to the schema](https://github.com/keighty/virtualplaybill2/blob/ad04bcde8ce00c5ce349f43d2e0cc50549c59df7/models/posts_model.js#L11) solved all my woes.

If you are trying to accomplish something similar, Heroku has articles on [uploading directly to S3](https://devcenter.heroku.com/articles/s3) using many different stacks.

