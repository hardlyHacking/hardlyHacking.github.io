---
layout: post
title: Surfing
date: '2018-04-28 09:12:32'
tags:
- Board Games
---

A little side project that I completely forgot about: [boredgames.io](http://boredgames.io/)

[Way back in 2016](https://notablog.xyz/2016/06/26/piti.html) I dabbled in Angular at a time when React.js was all the rage, but I never really made a full-fledged sample project in pure React. Most people like myself use React with Redux, but I decided that a Codenames clone was a great opportunity to build something that I would use on a semi consistent basis (it's the most popular game at virtually every party) and at the same time, learn real native React to get a better appreciation for the platform and its vanilla limitations (or lack thereof!).

Overall, it took me about [four hours](https://github.com/hardlyHacking/spymaster) for the original commit, though I have gone back and added a couple retouches.

I think people vastly overestimate the need for Redux as a whole. React, for most projects, is sufficient. I do like Redux's approximation of a strongly typed language - it offers all the things about Typescript in Angular 2 that I really liked but in the proper data flow model that React offers. Proptypes allow programmers to specify the type along with the optional / required nature of the particular variable which allows developers to think properly in terms of interfaces and data flow (which parts are absolutely vital, which are unnecessary), which seamlessly flows into a proper component encapsulation breakdown.

Ultimately, I stand by what I said in that 2016 article, and I think the industry largely agrees.

This project was very fun because it really did teach me how much even a simple four hour weekend project can cost as far as maintenance goes. Over the following weeks, I dealt with server deployment errors (Digital Ocean went down for some issue or another), feature requests (from far superior competitors like the far superior [horsepaste](https://www.horsepaste.com/)) which led me to refactor the whole codebaes and get rid of the two code model in favor of a single spymaster code, and numerous other design nits.

The keen observer will note that I still don't have the actual code to make entering a custom game ID working. This is intentional. `:)` My brother is off to college and majoring in computer science, and I wanted to give him an opportunity to work on a real website that we both use at family events, so this was his small starter task.