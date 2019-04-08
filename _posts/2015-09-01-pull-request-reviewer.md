---
layout: post
title: The PR Review Watcher, save time when reviewing code
subtitle: Simple automated checklist
categories:
- blog
catalog: true
date:       2015-09-01
author:     Tristan Bessoussa
header-img: /images.unsplash.com/photo-1485083269755-a7b559a4fe5e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1950&q=80
tags:
    - PR
    - Pull Request
    - Checklist
    - Review
---

Are you tired when reviewing code on a pull request, to spend too much time on (important yet time consuming) details, such as:

 * Is the code PSR valid ?
 * Did the developer thought adding the config key in the right file ?
 * Are the SQL migrations generated ?
 * ...
 * **Any recurrent requirements your applications needs developer to be reminded of ?**


We are trying to avoid those kind of situations:

![gif comment pull request PSR](/img/pr.gif)

Well, good news, the [PR Review Watcher](https://github.com/Yproximite/PRReviewWatcher) bot is for you.

It will **post a list of checks** you've defined as a Pull Request comment when a new pull request is created.
Simply install the project via composer, and use the interface to configure a list of checks you want to post on repositories. You can use different checks for each branch if you want to.

It can save time for your team by avoiding them to check rather insistently for common mistake and focus on code architecture, algoritmh, comprehension...

![gif comment pull request PSR](/img/screenshot-pr-watcher.png)

Feel free to [contribute](https://github.com/Yproximite/PRReviewWatcher) and to send PR's (the project is in Silex).

PS: The code style is just one example to illustrate the purpose of the project in a global way. Between you and me, on my team, there's a pre-commit hook to prevent dev from commiting non PSR2 valid code.
