---
layout: post
title: "Afraid of commitment"
date: 2014-02-04 08:04:27 -0800
comments: true
categories: git
---
### or, how to not fubar your git history

As with all the git lessons I have learned, this one started with a huge mistake. I had merged a commit to master that was breaking pages I didn't know existed. The sage developer sitting on my right shoulder told me that since I was unsure of how to fix the problem I should revert the offending commit forthwith. <!--more-->

I had two choices of commit to revert:

1. remove the offending commit from the branch history
2. remove the merge commit, where the broken code had infiltrated master

While the former is like a surgical excision of a moment in history (eternal sunshine of a spotless git history?), the latter will preserve your branch history, and the context surrounding the changes made. I like a lot of context, so I chose to revert the merge commit.

When you undo a merge, git doesn't know how to reassign the branches. Was the left one master? The right? Does git know left from right? Who is on first? Take the guesswork out of the equation

```bash
$ git checkout -b revert_branch_name
$ git revert -m 1 <SHA of commit to be reverted>
```

Warning: If you don’t make the new branch first, you will mess up your merge history on master. Trust me. Just make the branch first. Now you can ensure that your test suite will pass with the commit reverted before you do something silly, like merge even more breakage into master. Crisis averted.

Awesome.
