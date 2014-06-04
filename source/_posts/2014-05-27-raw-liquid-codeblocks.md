---
layout: post
title: "raw liquid codeblocks"
date: 2014-05-27
comments: true
categories: [liquid]
published: false
---

I thought that using handlebars ({{}}) or other special characters inside a liquid markup codeblock was impossible

{% codeblock Title lang:js %}
{{}}
>>
>>
>>

{% endcodeblock %}

https://github.com/imathis/octopress/issues/813

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

<!--more-->
