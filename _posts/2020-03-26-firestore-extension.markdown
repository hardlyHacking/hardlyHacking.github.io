---
layout: post
title: 'Firestore Date Converter'
date: '2020-03-26 00:29:03'
tags:
- Firestore
- Chrome Extensions
---

Way back in [November of 2018](https://blog.chander.app/2018/11/16/serverless.html), I was sold on the serverless future. And when it comes to Serverless, the best bang for your buck is Firebase. Between a generous free tier and a huge array of services that seamlessly transition to a broader array of GCP offerings, it's no wonder that so many people use it as the stating point for new projects and companies.

One thing that consistently irritates me, however, is the Firestore UI. As I keep working on side projects that utilize Firebase and Firestore, I'm using the brief periods of respite between them to focus on these grievances.

Today's issue: dates.

Firestore has 2 supported formats for dates, [Javascript Date objects](https://firebase.google.com/docs/firestore/manage-data/add-data), and [Firestore Timestamps](https://firebase.google.com/docs/reference/js/firebase.firestore.Timestamp). Yup, they not only have a `date` type, but also a `timestamp` type. Who made this decision? Why?

Javascript date objects are huge problems, obviously. Presumably your entire company / project won't _just_ be Javascript, so when it comes to exporting to Big Query or any other place, vanilla JS dates simply won't work. Timestamps are a little better, but of course that means you have to convert to a date in your client code wherever that's used, and while easier to export to Big Query, it's still not actually ideal. I think it's meant for people who legitimately need the nano-second level precision that it offers, but I don't know who actually needs this... and if you did, why you'd use something like Firebase.

Anyway, almost everyone I've met prefers epoch milliseconds - it's tried and true, and it just works. Everyone and their mother can convert to and from epoch milliseconds, so why not just use that? It's what we ended up standardizing around at Skip - but there's only one problem. It's not very human readable. Both the `date` and `timestamp` types are auto-converted to human-parseable date strings in the Firestore UI.

Well, with [this new chrome extension](https://chrome.google.com/webstore/detail/firestore-utils/oebknnjfpdekbaalhncaifcddmeijlkn) I wrote, that problem is solved.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Exporting Javascript Dates / Firestone Timestamps out of Firestone is painful - I tend to use epoch millis wherever possible, but millis aren’t human readable. I made a quick chrome extension to solve this problem.<a href="https://t.co/JM0oBDouHE">https://t.co/JM0oBDouHE</a><a href="https://t.co/kxeqdgJG99">https://t.co/kxeqdgJG99</a> <a href="https://t.co/gMLG0iBPDU">pic.twitter.com/gMLG0iBPDU</a></p>&mdash; Chander (@chander) <a href="https://twitter.com/chander/status/1243068444364271617?ref_src=twsrc%5Etfw">March 26, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>