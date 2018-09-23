---
layout: post
title: Leaving Twitter
date: '2018-08-17 19:16:13'
tags:
- Startups
- Twitter
---

After over four years at Twitter, I've finally decided to ["leave the flock"](https://twitter.com/chander/status/1025531448957198340). I wanted to wait for a couple weeks for my emotions to settle before talking about this, so that I could really evaluate whether my reasons for leaving and whether they were justified.

I've felt for a really long time that I wanted to try a startup. Working at a big company is _very_ comfortable; free food, amazing perks, and all the great infrastructure of a big company. Twitter and all the other big silicon valley companies do a great job of making it very difficult to leave, and that's certainly not a bad thing.

I was telling myself every quarter for the past two years that this would be my last quarter, but inevitably something came up. Twitter gave me an unparalleled opportunity to grow as an engineer and learn so many different technologies. The product scope is so large and so immense that there's always something new to learn or do, and I'm so glad I took full advantage. I changed teams often and frequently whenever I felt myself stagnating, and as a result I got to see a much larger slice of the company than most people would.

I started off my Twitter career by interning on the Capacity Engineering team whose charter was to predict server demand for the company (and internal services within the company) and right-size our hardware fleet (as Twitter manages its own datacenters). This was such a unique role relative to most software engineering teams and gave me a phenomenal perspective into hardware, supply chain, and machine learning fields. Most importantly, I got pretty deep into Hadoop and Scalding.

After a while, I moved to the Commerce team seeking a more product-centered role. I picked up some iOS and more traditional backend skills here, along with learning how to work on a product-focused team with more traditional sprint cadences. I switched thrice more to the B2C team, then to DM backend, and eventualy to DM client where I found my final home.

I learned a lot and made great friends, but throughout my time at Twitter, I felt a couple consistent themes which ultimately led me to resign.

## Iteration Speed

Around Q3 of 2016, I discovered how broken our Hashflags infrastructure was. For those who don't know, hashflags are little icons that appear after custom Hashtags that are planned and launched by Twitter. Some of these are paid for by Disney (for example, if a new SpiderMan movie was launching, perhaps typing `#SpiderMan` would show a custom spiderman emoji on the client), while others are planned and executed by Twitter (think `#LoveIsLove` or `#BlackLivesMatter`).

Hashflags were all stored in this one large XML file, and they'd have to be manually entered. We had one full-time contractor whose only job was to input these hashflags and commit them to version control. Anytime we'd have a large event like the Olympics or World Cup (each had ~500 hashflags), we'd hire two more contractors for the 4-6 weeks preceding this event.

This also led to some pretty crazy side effects, like

a) an engineer had to be present any time a Hashflag needed to be added or deleted

b) adding or deleting a new hashflag was *extremely* error prone (think typos, etc)

c) the whole process took too long - since contractors were paid hourly, we'd artificially limit the SLA to add a new hashflag to 5 business days

Since it was hackweek, I decided to automate the whole process by building a protal to do all of this. It would take a PNG from design studio (or our external customers), produce all the variants needed (4 different sizes for android based on DPI, 3 different ones for iOS, and a final one for Web). It edited the XML file as well, and could take one or more hashtags given a particular image. It would then add the images / comment on JIRA, send an email as confirmation, and upload everything.

This moved our SLA from ~5 business days / however long it took manually, to just 5 minutes. It also allowed us to move those contractors to do more valuable work, as now design studio could create / delete hashflags on their own, even without an engineer present. (This was particularly valuable for a hashflag we launched on BastilleDay. This was planned to be a celebratory hashflag, but unfortunately there was a terrorist attack that day, and the design led users to think Twitter was celebrating the terrorist attack. All the engineers were offline / on vacation, but because of this new tool, design studio could delte it within five minutes without ever having to bother engineering.)

But this happy story was not without the typical problems that plague big companies. The stickers team had this on their roadmap for 2 quarters from now, and they were quite upset that I was doing this. There was significant pushback, but ultimately sales and brand strategy teams found it so valuable, that I went ahead and shipped this tool anyway.

(By the way, it's since been two years and the stickers team still hasn't done the "real", "non-hacky" solution.)

Almost every feature I would try and ship at Twitter that wasn't narrowly tailored to the scope of my team's work was met with significant pushback, even if I knew others teams did not necessarily have it on their roadmap. And yet, these projects were often the most fun because I knew how much better I was making the lives of other employees!

Twitter has a tendency where perfect becomes the enemy of good - the question is rarely "does this make us better off?", but almost always "is this the perfect solution?" And this is absolutely exhausting from a motiviation and morale perspective. Initiative should always be rewarded, and if it's not, I tend to believe that the company has over-hired or is overly-political.

## Impact and Responsibility

Seeking to make a huge and lasting impact should be, I believe, the forefront of every engineer's goal. Building a perfect system that doesn't make anyone's life better is meaningless (as is refactoring / addressing tech debt that doesn't improve the business). It was originally why I moved to the DM team. DMs is one of the 4 core tabs at Twitter, and is extremely underresourced relative to the other tabs.

And even with DMs, Android needed bodies more than anyone. Almost 45 million people use DMs on Android every day, and there were (including me) only three engineers. That's a scale of impact that's difficult to find at a large company like Twitter, and I'm extremely glad I joined the team.

Even though I was one of the core engineers, I found myself unhappy after a while. We were doing marginal improvements to a product (an occasionally those marginal improvements would be vetoed), but ultimately I felt that nothing I did was _essential_ to the company. If I took off a couple weeks, nobody would really notice.

And that's not a bad thing!

But at the same time, it's not for me. I felt I had hit my limits in terms of growth and responsibilities. There were always more senior people who would get big projects, and nearly everything I did that I was most proud of was something I undertook on my own. I owned all of Hashflags, nearly end to end. I managed all of our Chrome Extensions at Twitter, and developed several chrome extensions that became indispensible to engineers throughout the company. In each case, this was big enough to get a shoutout directly from the Bluebird director of engineering, or in the case of Hashflags, a presentation in front of the entire company. But it was never met with an increased responsibility in the core areas of the team.

And I think that's what bothered me most. I think for my position, which is a bit of a hybrid role, there just isn't room at a big company like Twitter. I'm a bit of a weird backend/Android developer who likes to find opportunities to speed things up and help developers, and that doesn't really fit neatly within any one team's core purview. Big companies like Twitter need super disciplined, super focused engineers who maintain focus on the largest problems, and I was not senior enough or disciplined enough to ever get that role.

So it became a bit of a negative feedback loop for me.

I desparately wanted to make an impact, but I wasn't senior enough to get a strong tech lead role. So I ended up grasping at side projects that I saw lacked ownership as a means of substituting that, but this inevitably led me to have a reputation as unfocused and undisciplined. But this was the only way I could really ship something of value, which... led to the start all over again.

## Time to Move

Moving was the hardest part. As I stated before, Twitter didn't necessarily do anything _wrong_. My former coworkers are unbelievably brilliant, thoughtful, and kind. Twitter paid me well, and I enjoyed my time there. And it made it very difficult to leave.

But the longer I stayed, the more antsy I got. Time is the biggest premium, and and I felt I was wasting my potential for fear that I would never be able to get back into a company as technically and financially as sound as Twitter if I ended up going the startup route. I'd have to cook my own food, downsize on some expenses, and probably sell my car to make ends meet.

But at the end of a long career, I didn't want to have regrests. I never wanted to look back and think, "Yeah, I would have been great at startups." And I think, as hard as it is to let go of a steady paycheck from a prestigious company, it's most costly in the long run to avoid trying for fear of failure.

Lest it be misunderstood, I'm incredibly grateful to Twitter. They took a shot on an intern who didn't really have any coding experience at all, and somehow hired the intern full-time after that intern brought down the entire website for 4 hours. Every time I wanted to transition to a role I had no idea about, they were completely supportive and helped me succeed.

Twitter is and will continue to be a phenomenal company, just no longer the best fit for me.