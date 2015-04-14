---
layout: post
title: "What's it like to be a developer?"
date: 2015-04-11
comments: true
sharing: true
categories: [jobs, interviewing, programming]
---

My manager has invited me to help her give a talk to students in a Web Development program. I have been working in the field for about 18 months, and a lot of the uncertainty is still fresh in my mind: what is it like to be a programmer? How can I get a job as a junior? What can I do now that will help me in my first job? I didn't know many people in the industry, and was unaware of the vast meetup community[^1]. Answers to these questions were difficult to find online, so over the next few posts I will make an attempt.
<!--more-->
<hr>

### Meetings: Important communication tools.
Programming is mostly about decision making, and not all decisions can be made alone. Questions like, what technologies to use, how you should spend your time, and how to scale, require meetings with managers and other teams.

* **Sprint planning** is a weekly meeting where the team gets together with managers to prioritize tasks for the week. You look back on last week, and either carry over tasks you didn't accomplish, or re-prioritize based on what has arisen in the meantime.

* **Standups** happen daily, and should only last around 10 minutes. You each take a turn sharing what you worked on yesterday, what you are working on today, and whether you are blocked by anything. It is important to speak up when you are stuck! Sharing your blockers will usually result in help; someone may have encountered this problem before, have a new approach to suggest, or have insider information that can get you to a solution.

* **Demos** will happen on a weekly or bi-weekly basis, and are an opportunity to share your accomplishments. While many stakeholders are involved in the project and sprint planning, they are often disconnected from the work as it is being performed. Regular demos help to keep them in the loop, and are an opportunity to explain both progress and unexpected delays.

* **Code reviews** can be formal or informal, and allow your team to gain consensus on style, design, performance, and testing. Your code design decisions will have an impact on the project as a whole, so it never hurts to get feedback from a teammate before you ship it. Readability is more important than cleverness, and is a kindness to future maintainers.

A portion of your week will always be spent in meetings. Try to identify when your most productive hours are in the day, and keep that time free. For example, if you lump meetings around lunch (none before 11am or after 2pm) you have a solid chunk of time in the morning and the afternoon to focus effectively.

### Stories: How big ideas are turned into actions.
An idea like "improve the user's experience of widget B" is a goal, not a task. It can be broken down into **stories** such as: *identify the problem*, *brainstorm solutions*, *design the new widget*, and *implement the new widget*. Each of these stories are actionable: there is a clear path from start to finish. Use the [single-responsibility principle](http://en.wikipedia.org/wiki/Single_responsibility_principle) in your stories as well as in your code: if you use the word AND to describe the task, that is a hint that you should have two stories instead of one. Most companies will use a task management, or **ticket** system such as Pivotal Tracker or Jira, where you can see what work is assigned to you, and where managers can check in on your progress.

### Feature Work: What should be built?
Product managers have their eye on the market. They know what features our customers want, and they know what our competitors offer. A product manager will build a product road-map with a long schedule (think months and years, not weeks). The team will sit down with the feature requirements and a first-draft design, break it down into stories, and estimate the time/complexity of each story. Managers then apportion those estimates into chunks of work -- in **Agile**, these chunks are laid out in **sprints[^2]**.

Defining work in this way lets you identify what can be done in parallel and what needs to be in place before the next task can be accomplished. It also gives project managers the information they need to make decisions about what to sacrifice in order to meet a deadline. If the project is over the time budget, and gold-plated widget A needs features 1, 2, and 3, then maybe you can ship the widget with only 1 and 2, but save 3 for later. Sometimes you have to compromise on feature completeness just to get it out the door.

### Troubleshooting and Maintenance: Detective work.
Finding a bug is like being a detective: you gather clues, follow leads, and occasionally end up lost in a blind alley. A single line of code can hold multiple bugs (Pro tip: watch out for Regexes[^3] -- they are a great place for bugs to hide). In a code base with hundreds of thousands of lines, you will inevitably spend more time hunting and fixing bugs than writing new code.

### Deploying code: Sending it into the wild.
Running an application in production is not the same as running it on your computer. Your application may need to serve thousands of requests per second, and a single instance running on a single server won't cut it. There is a whole stack of management software that you would do well to be familiar with, and having at least a passing familiarity with [Jenkins](https://jenkins-ci.org/), [Capistrano](http://capistranorb.com/), and [bash scripting](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html) will help you when it is your turn to carry the pager.

### Pager duty: Evil alarm clock.
When you write software that your customers use around the clock, you have to be there when things break, no matter what time. Most companies will institute a rotation to share the burden of vigilence around the team. Having great documentation about what to do in an emergency is essential to finding a clear path of action at 3am. As with anything, the more you prepare, the higher your confidence level will be.

The job is far from routine because of the diverse set of problems to solve. I have become comfortable grabbing a story without already knowing the answer, partly from knowing that if I get stuck I am no more than a "Hey Bryan," or "Hey KWu," away from someone who wants to help, even if it is at 3am (thanks again, Vince!).

[^1]: If you are in Portland, Oregon, checkout [Calagator](http://calagator.org/). Otherwise, try [Meetup.com](http://www.meetup.com/)
[^2]: Read more about [Agile methodology](http://en.wikipedia.org/wiki/Agile_software_development)
[^3]: Regex (short for regular expression) is a search pattern based on a sequence of characters.
