---
layout: post
title: "estimate != commitment != target"
date: 2015-10-31
comments: true
sharing: true
categories: [read]
---

Software estimation is hard, but I didn't really understand why until I started reading [Software Estimation: Demystifying the Black Art](http://www.amazon.com/Software-Estimation-Demystifying-Developer-Practices/dp/0735605351) by Steve McConnell. It was published in 2006, and is very much still relevant -- there are more ways to do estimation wrong than to do it right.

The biggest "aha" moment for me was learning the difference between estimates, targets, and commitments. <!--more-->I always thought that estimates were commitments and treated them like targets. If a task took longer than my estimate, I would worry that I had blown the project plan. The source of my stress was really semantics, and a deep misunderstanding of project plans in general.

#### 1. An estimate is a range where there is a 90% chance of finding the completion date
An estimate is not a single point in time -- that is a target. There is uncertainty in software development, and uncertainty about the uncertainty.
> There is a limit to how well a project can go but no limit to how many problems can occur.

Estimates are probability distrubutions of possible outcomes:

{% img left /images/151031-software-estimation/estimation-chart.png %}

Most of my experience in estimating projects has been to break a feature down into small tasks and estimate those. Tightly defined requirements for a task make it easier to estimate, but there are always complications that can throw things off track. Thinking about an estimate as a range of possibilities between the best and worst case scenarios reduces stress about being right the first time.

#### 2. A commitment is a promise to deliver defined functionality at a certain level of quality at a certain date.
You can use estimates to plan commitments, but not the other way around. If ten features take a year to develop, but your customer needs to have something in six months, you can commit to delivering four or five features by that target, but there is no way you can deliver ten.

#### 3. A target is a single point in time, and is usually determined by business needs.
A target has more in common with a commitment than an estimate -- it can have no bearing on the time estimated to complete a project, but will often define the envelope of time. Like in the previous example, the target for delivery to the customer was six months, but the commitment could be four or five features, depending on the estimates for each feature.

Estimation must come before planning, and before you really understand all of the requirements for the project. More often than not, events that happen during the project will have a big impact on the assumptions made to estimate the project at the beginning. Estimates are hard, but McConnell has some awesome advice on how to get it right.
