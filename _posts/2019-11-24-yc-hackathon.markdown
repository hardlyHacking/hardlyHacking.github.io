---
layout: post
title: YC Hackathon
date: '2019-11-24 09:29:22'
tags:
- YCombinator
- Hackathon
---

This past weekend I was fortunate enough to attend the YC Hackathon, where my team placed in the top ten.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Had a blast at the <a href="https://twitter.com/ycombinator?ref_src=twsrc%5Etfw">@ycombinator</a> hackathon! Team CredZ placed top ten! <a href="https://t.co/BAd0x9ixIv">pic.twitter.com/BAd0x9ixIv</a></p>&mdash; Chander (@chander) <a href="https://twitter.com/chander/status/1198485767388987392?ref_src=twsrc%5Etfw">November 24, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

For those unsure or just considering, I would highly recommend it. The stakes are pretty low, as it's just one day, and there's no guarantee of being accepted into YC which makes people more friendly and less competitive (though the uber-competitive types are still around). Overall, I met some awesome people and learned a lot!

The format is basically that everyone arrives around 6PM on Friday, where they go over the rules. Around 6:30 they give a chance for anyone who doesn't have a team or wants more members for their teams to pitch their ideas, and they then allow folks an hour or so to join teams while eating dinner.

In my case, I had a team already formed with my coworker Sara Garner from Stripe, and her friend Jill (who worked with her at HouseParty previously). The day I arrived, another Stripe (Tushar) actually reached out to me and mentioned he wanted to join as well, so with that our team was formed!

We had been discussing various ideas during the weeks leading up, but the idea we ended up going with was actually something I thought about on the Caltrain going down, an hour before the hackathon. With some refinement and ideas from the group, we were off!

Our team, CredZ, is the credit system for the internet - think of Reddit Gold, but with revenue sharing. And instead of just working on Reddit, it works on any and all websites. I noticed that there's a lot of really useful content being produced online, but not necessarily a great way to reward it. You can sign up to endorse a contributor as a person (Patreon), or have high thresholds (Buy Me a Coffee), but there wans't really a way of telling a creator with high fidelity that _this_ particular content was something I loved. Imagine you found a blog post that changed your life, or maybe a Stack Overflow answer that saved you four hours of debugging. Why not give the author 15 cents? Or maybe a tweet was absolutely hilarious - give the author 50 cents.

Small microtransactions lower the barrier, but more importantly, this system allows you to divorce yourself from the author. You're not endorsing _them_ - you're endorsing this piece of content. Patreon tries too much to focus on recurring monthly cycles with access to more content, but the barrier for that is often too high. There's tons of small acts of kindness / novelty / beauty happening on the internet that should be rewarded as well.

This system also turns spam on its head - if spammers want to credit some content, they at least have to pay for it. Ultimately though I think this would lead to much higher fidelity signals. If you saw a tweet with 200k likes and no credits, but another with 180k likes and maybe 10 credits, it's far less likely that the latter one was the product of spammers (we think).

At the core of our business was Stripe. (Three Stripe employees walk into a room...) With Stripe Connect, we could make a merchant account for all contributors, so when someone donates, if we already know the associated merchant account we would auto deposit into their account. If we didn't know who the person was, we'd have to authenticate an onboard them. The onboarding mechanism would vary based on the website. For example, if it was a tweet that was credited, we'd send the author a DM and guide them to onboard onto our system, if they ever wanted to actually cash out their money.

So here is what a typical flow would look like. You're browsing the web for some awesome content, and you find some. You decide this tweet is more hilarious than a simple "like" would warrant. So you open the CredZ chrome extension, and immediately you can see how many others have credited this same tweet.

![](/images/2019/11/initial_screen.png)

You decide this CredZ extension is pretty cool, so you make a new account.

![](/images/2019/11/login_screen.png)

After inputting your card the first time, the extension naturally remembers it.

![](/images/2019/11/saved_card.png)

You can then go onto the [CredZ website](https://credz-io.web.app/) to see all the other donations in realtime.

![](/images/2019/11/credz_website.png)

Going to Stripe, you can actually see the payment that went through, along with the various fees, and the net amount going to the "merchant" (the content creator in this case).

![](/images/2019/11/stripe_payment.png)

And finally, in the Stripe merchant account in Stripe Connect, you can see the total balance this merchant has acquired thus far.

![](/images/2019/11/stripe_connect.png)

Around 4PM, the hackathon issues a code freeze and they begin demos in four tracks. Usually they have eight judges (two judges / track), and they split up all the teams into the four tracks and demo. This year, unlike the previous year, they were focused more on pure functionality - no powerpoints, slides, etc. Just the demo actually working. Each team was given exactly 90 seconds for the demo, and afterwards the judges might ask one or two questions. We were actually the very first team to go, and we found out that the judges were increasingly lax as time went on about the time limit, so while we were forced to stop at 90 seconds on the dot, some others actually went on for a bit longer. 90 seconds was pretty rushed in my opinion, as we couldn't even show off all the features we build (login / logout flow, trending donations pages, etc), and even typing a new card into the chrome extension was a bit tough.

In retrospect, we focused a bit too much on building the product and not enough on practicing our pitch. None of our team were going to necessarily quit our jobs even if we won though, so our focus was mostly on having some fun and learning something new, rather than ironing out a perfect startup pitch and 'winning'.

After that, we finally got to relax and unwind. The rest of the demos took another two hours, during which time we mostly ate, played some basketball and cornhole (I won all three games of 'HORSE', thank you 5th grade basketball camp), and talked to the other groups. After that, the judges consulted amongst themselves to select the top ten groups.

The diligent and ambitious groups were practicing their pitches throughout this period, in the hopes that they won. Our group naturally assumed there was no way we were going to win, so we simply had fun... Until they announced our group as the very first to present. The subsequent final demo was even worse than the first one, if that were possible - and we didn't really learn our lesson to not try and show off all the features.

Still, overall top ten wasn't too bad! We had a lot of fun and really enjoyed the experience. We fortunately decided not to stay up all night - in my opinion, sleep deprivation doesn't really help, particularly since there's usually some last minute regressions in a hackathon, and the ability to quickly debug is crucial. Also, the event went on until 10PM, so not sleeping would have been an exceptionally bad decision. Some folks did end up sleeping in the building on sleeping bags, but I decided I was too old for these shenanigans and just slept on my parents couch, 15 minutes away from YC headquarters.

After the ten finalist demos were completed, the judges again convened before announcing the winners. There were three honorable mentions, and one winner who got a YC interview. Then it was a happy hour for another 90 minutes before YC closed for the night. An exhausting but very fun weekend!

Should you care, you can find our beautiful code [here](https://github.com/c10r/yc-hackathon-2019).