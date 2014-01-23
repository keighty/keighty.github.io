---
layout: post
title: "git retrieve: a drama"
date: 2014-01-23 20:47:46 -0800
comments: true
categories: git
---

Have you ever deleted a file on impulse, only to wake up with remorse later? It doesn't matter if later is the next morning or the next week, thanks to git you have the power to reanimate gone-but-not-forgotten files. Nothing is ~~ever~~ EVER lost with git. So how do you retrieve a file that has been deleted in a previous commit?

<!--more-->
[stackoverflow](http://stackoverflow.com/a/953573) has a few suggestions, and I like this one the best:

###Git Drama in Three Acts
```bash Act 1 Scene 1: foo.bar Folly
$ cd ~/very_important_project/
$ touch readme.md index.html foo.bar
$ git commit -am "These changes are AWESOME."
[master (root-commit) 9155e0b] These changes are AWESOME.
 0 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 foo.bar
 create mode 100644 index.html
 create mode 100644 readme.md
```
but wait! ``foo.bar``?! that is just crazy talk?

```bash Act 1 Scene 2: Regret
$ git rm foo.bar
rm 'foo.bar'
$ git add -u
$ git commit -m "I have made a huge mistake."
[master b045a52] I have made a huge mistake.
 0 files changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 foo.bar
```

Everything seems back to normal!

```bash Act 2 Scene 1: I have made good life-choices
$ echo "#I rock and I am smug about it!" > bar.rb
$ git commit -am "This work is the BOMB."
[master 3535f28] This work is the BOMB.
 0 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 bar.rb
$ cat foo.bar
cat: foo.bar: No such file or directory you heartless creature!
```

but wait! there is no bar without a foo! where is my foo.bar? How can I find it? I have to find the commit where it was deleted, but how? What if I list out the commits that include a deleted file?

```bash Act 2 Scene 2: Regret
$ git log --diff-filter=D --summary
commit b045a528b4a0824f63562cad867b264983b32c7c
Author: keighty <keighty.leonard@gmail.com>
Date:   Wed Jan 22 20:46:16 2014 -0800

    I have made a huge mistake.

 delete mode 100644 foo.bar
```

There you are! in commit b045a528. The summary option shows the details, so you can see what files were deleted, when, and by whom. In some cases, there may be more than one change in a commit, so using ```commitSHA~1 <filename>``` will pluck the one precious deleted file from the commit and restore it to your working tree.

```bash Act 3: foo.bar's Return
$ git checkout b045a528~1 foo.bar
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
# new file:   foo.bar
#
$ git commit -am "Together at last, together forever."
[master 8492eac] Together at last, together forever.
 0 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 foo.bar
```

EXEUNT.