---
layout: post
title: "formatting numbers the easy way"
date: 2014-08-23
comments: true
categories: [d3, javascript]
---

D3 has an awesome number formatting function that I discovered only after hacking my own. My first approach was to use string and array manipulation to convert 1234567890 to 1,234,567,890:

{% raw %}
```javascript
function numberSmoothing(value) {
  // split the number into a character array and reverse it
  reversedNumber = (value).toString().split('').reverse();

  // ["0", "9", "8", "7", "6", "5", "4", "3", "2", "1"]

  // push the numbers onto another array in groups of 3 and add a comma
  chunkedNumber = [];
  while(reversedNumber.length > 3) {
    var numberChunk = reversedNumber.splice(3);
    chunkedNumber.push(reversedNumber.join('') + ',');
    reversedNumber = numberChunk;
  }

  // push the remaining digits onto the array
  chunkedNumber.push(reversedNumber.join(''));

  // ["098,", "765,", "432,", "1"]

  // reverse each of the strings in the array and rejoin them
  for(var j = 0; j < chunkedNumber.length; j++){
    chunkedNumber[j] = chunkedNumber[j].split('').reverse().join('');
  }

  // [",890", ",567", ",234", "1"]

  return chunkedNumber.reverse().join('');
}

> numberSmoothing(1234567890)
"1,234,567,890"

```
{% endraw %}

While it was a fun mental exercise to implement number formatting, I stumbled across this handy d3 function after I was already done:

{% raw %}
```
> var format = d3.format(",");
> format(1234567890);
"1,234,567,890"
```
{% endraw %}

As it turns out, any way one might want to format a number has [already been implemented in d3](https://github.com/mbostock/d3/wiki/Formatting#d3_format). It is similar to the mini-formatting language from python:

{% codeblock %}
[​[fill]align][sign][symbol][0][width][,][.precision][type]
{% endcodeblock %}

{% raw %}
```javascript
// add commas for the thousands separator
> var format = d3.format(',')
format(1234567890)
"1,234,567,890"

// add a sign for positive and negative numbers
> var format = d3.format('+,')
> format(1234567890)
"+1,234,567,890"

> format(-1234567890)
"-1,234,567,890"

// add a currency sign
> var format = d3.format('+$,')
format(1234567890)
"+$1,234,567,890"

// as a percentage
> var format = d3.format('p')
format(1/6)
"16.666666666666664%"

// as a rounded percentage
> var format = d3.format('%')
> format(1/6)
"17%"

// as a percent rounded to a single sig fig
> var format = d3.format('.1%')
> format(1/6)
"16.7%"

// as a rounded value
> var format = d3.format('.2r')
> format(123.456)
"120"

>format(123456789.66)
"120000000"

// as a rounded, comma separated value
> var format = d3.format(',.2r')
> format(123456789.66)
"120,000,000"
```
{% endraw %}

Awesome.