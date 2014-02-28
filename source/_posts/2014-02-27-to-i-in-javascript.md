---
layout: post
title: "string conversion in javascript"
date: 2014-02-28
comments: true
categories: [I didn't know that, javascript]
---

Almost every day, javascript blows my mind. Today I discovered that you can convert a string to a number simply by prepending a +:

```
> typeof(+"this is a string")
"number"
> typeof(+"100")
"number"
> +"100" + 50
150
```

Awesome.