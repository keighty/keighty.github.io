---
layout: post
title: "flash your local collection"
date: 2014-05-26
comments: true
categories: [meteor, javascript]
---

Rails allows you to pass messages to the user between actions ("This [ActionDispatch::Flash](http://api.rubyonrails.org/classes/ActionDispatch/Flash.html) will self-destruct"), and because Rails, all the message generation happens server side:
{% codeblock flashy_controller.rb lang:ruby %}
def show
  flash[:notice] = "You dropped your packet."
end
{% endcodeblock %}

Meteor is mostly a client-side application framework, and you can implement a similar message system, which will never touch the server, by defining a collection that is local to the browser.<!--more-->

### 1. Create a local collection
{% codeblock /client/errors.js lang:js %}
Errors = new Meteor.Collection(null);
{% endcodeblock %}

Passing `null` into the Collection declaration tells Meteor that the collection should not be extended to the server -- it creates an "unmanaged (unsynchronized) local collection" ([docs](http://docs.meteor.com/#meteor_collection))

### 2. Throw an error whenever you need to
{% codeblock /missionCritical.js lang:html %}
if(error){
  Errors.insert({message: error.reason});
}
{% endcodeblock %}

### 3. Create a template for displaying the error
{% codeblock /client/errors.html lang:js %}
{% raw %}
<template name="errors">
  <div class="errors">
    {{#each errors}}
      {{> error}}
    {{/each}}
  </div>
</template>

<template name="error">
  <div class="alert alert-error">
    <button type="button" class="close" data-dismiss="alert">&times;</button>
    {{message}}
  </div>
</template>
{% endraw %}
{% endcodeblock %}

### 4. Create a helper method for serving the errors to the template
{% codeblock /client/errors.js continued again lang:js %}
...
Template.errors.helpers({
  errors: function() {
    return Errors.find();
  }
{% endcodeblock %}

Every template that contains the handlebars `{% raw %} {{> errors}} {% endraw %}` will now display whatever errors have been saved to the session collection.

Awesome.

###Resources
[discovermeteor.com](https://www.discovermeteor.com/)
[Meteor docs](http://docs.meteor.com)