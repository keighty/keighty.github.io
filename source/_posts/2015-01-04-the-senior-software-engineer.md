---
layout: post
title: "TDD and the senior software engineer"
date: 2015-01-04
comments: true
categories: [read, tdd]
---
Many developers embrace test-driven development (TDD) as an ideal but rarely put it into practice. I often find myself writing code that works and then writing tests that exercise it to ensure that its behaviour doesn't break when changes are made. Writing tests first is a great way to organize your approach to solving a problem, and in [__The Senior Software Engineer__](http://www.amazon.com/Senior-Software-Engineer-Practices-Effective/dp/0990702804/ref=sr_1_1?ie=UTF8&qid=1420403139&sr=8-1&keywords=on+being+a+senior+engineer), David Copeland describes how TDD will help you grow your career as well.
<!--more-->

A senior software engineer is efficient, and delivers a quality product in a timely fashion. {% pullquote left %}{"Effective problem solvers first spend time understanding the problem space and then design a plan"}: documenting feature behaviour in a test suite allows you to plan your implementation. Code maintenance and modification are also more efficient when you have a good test suite. If the code has been built using TDD, its behaviour is already well tested. When someone needs to build on top of your feature, the tests will protect it and eliminate the worry of causing regressions (win-win).
{% endpullquote %}

Explicitly describing your plan in a test suite has another benefit that I had not considered before, and that is making you resilient to interruption. If your plan is not layed out on paper, you are maintaining it all in your head, and you have to hold on to a lot of context in your working memory. {% pullquote %}Every time you are interrupted, you have to dump things out of your working memory, and reloading the problem space takes time and energy. You won't suffer interruptions well if reloading the context takes 15 minutes each time. You will become unresponsive as you block out the outside world, trying to maintain your context.

{"TDD can help you structure your coding task so that it has natural breaking points. If you are interrupted while trying to fix a failing test, you know exactly where you can pick the task up again."} If you are interrupted after getting a test to pass you can easily read through your test suite to regain your context and pick up the next task.
{% endpullquote %}

Time and context management are essential skills for becoming a senior software engineer, and I am going to try using test driven development to become more interruptable and more productive.

<!-- RESOURCES
* http://act-r.psy.cmu.edu/wordpress/wp-content/uploads/2012/12/448preparing.to.resume.pdf
* http://interruptions.net/literature/Fogarty-CHI05-p331-fogarty.pdf
* http://contenthere.net/2012/10/what-it-takes-to-be-a-senior-engineer.html
* http://www.amazon.com/Senior-Software-Engineer-Practices-Effective/dp/0990702804/ref=sr_1_1?ie=UTF8&qid=1420403139&sr=8-1&keywords=on+being+a+senior+engineer
* [The Senior Software Engineer](http://www.amazon.com/Senior-Software-Engineer-Practices-Effective/dp/0990702804/ref=sr_1_1?ie=UTF8&qid=1420403139&sr=8-1&keywords=on+being+a+senior+engineer) by David Bryant Copeland

OUTLINE
Test-driven development can help you grow your career.
1. test-driven development is an ideal that many strive for and few embrace.
2. many developers embrace the ideas of test-driven development, but few practice it rigorously.
3. writing tests is important to delivering quality, robust code, and is essential for writing code that you ever hope to change.
4. did you know that test-driven development can help you grow your career as well?
-->
