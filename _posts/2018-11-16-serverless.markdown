---
layout: post
title: Serverless Architecture
date: '2018-11-16 21:16:28'
tags:
- Serverless
- Google Cloud Platform
---

## Serverless Is the Future

Since joining [Skip Scooters](https://skipscooters.com), I've learned more than I possibly could. Startups are an investment in yourself, in many ways. Because they don't have the resources to properly staff, it means that everyone is assigned roles larger than what they're truly ready for. There is no dedicated ops person, or QA team, or often times even an EM - instead, an IC takes on all of these roles. Not only that, instead of a senior engineer getting the best projects, often times there is only *one* engineer - you - and the number of projects you get is often ten times more than you would get at a big company.

All of this forces you to learn and grow faster than you ever could at a big company.

But nothing has been as revolutionary to me as serverless - specifically, Google's [Cloud Functions](https://cloud.google.com/functions/). The difference was night and day - akin to the first time I programmed in Python after years of C/C++ in college. It was a *wow* moment, and now I can't go back. The entire GCP ecosystem tightly integrated, but the benefits of serverless by itself were mind blowing to me.

At Twitter, we managed everything ourselves. Most of this was because Twitter was founded well before the inception of services like AWS or GCP, and even further before anyone truly brought serverless computing into being. So Twitter managed its own datacenters, its own hardware, its own ops/SRE teams, and its own containerizing framework, Aurora / Mesos, which it eventually open sourced. At Twitter, *nothing* was free, and *nothing* was out of the box.

Having worked on a business that runs *entirely* on Google Cloud Functions, let me tell you: I have seen the light, and serverless is indeed the future.

Google Cloud Functions (henceforth GCF) provides searchable logging, good observability, a neat integration into stack driver, the ability to do partial deploys, seamless database management with a pretty good UI, and security instrumentation all for free, out of the box! Skip's run rate is in the tens of millions of dollars and we have a grand total of two -- *two* -- backend engineers running the entire thing! It would never have been remotely possible without the insane amount of features GCP provides for free.

Since load balancing, routing, and networking are all basically taken away, it means when I write a new feature I'm truly *just* writing the business logic and nothing else. This allows us to move insanely fast; it took me a day to write the new promo codes feature, and another day to write almost all of our geofencing logic. When you're allowed to write exclusively business logic and examine just the meat of the problem, not only does your company save an enormous amount of money, engineers are also unable to procrastinate by solving fake problems. This was one of my hallmark bad habits at Twitter - I couldn't figure out X, so instead I'd clean up all this other tech debt and pretend I had a productive sprint. Wasn't the company's main priority X, and shouldn't I have focused on that and nothing else? Yes, and yes.

With serverless, a lot (not all, but a lot) of that extra baggage is just stripped away and moved onto Google's plate.

## Tips for Serverless on Google Cloud Functions

### Typescript

My first week at Skip, having seen our horrible 5000+ line single 'index.js' file that contained the entire backend, I not only refactored everything into a meaningful folder setup with subfolders based on features and product areas, but I moved us to Typescript. (The codebase was small enough at the time that this could be done in about two days, with a couple more days for testing.) This has proven to be one of the best decisions we could have made.

At the time, GCF only supported Node 6, with es5 Javascript features. We had a pretty standard multi-step transcompiling process that allowed us to write in es2017, but with Typescript, that was all simplified into a single tsc command.

Compile time errors are a wonderful, wonderful thing, and I highly recommend every backend, if possible, move to a strongly typed language. But GCF only supported Javascript at the time (and now only Javascript and Python), and the advantages of GCF strongly outweighed the disadvantages of Javascript, especially once we moved over to Typescript.

The other great thing about a modern programming language like Typescript is that it has all the things you'd expect - decorators, classes, and so much more. Typescript helps guard against run-time type erasure by using classes, so you can decorate your functions in a really nice and fancy way. For example, most of our API endpoints look something like the following:

```typescript
type Role = 'ADMIN' | 'EMPLOYEE'

class Location {
	readonly lat: Number
	readonly lon: Number

	constructor(lat: Number, lon: Number) {
		super()
		this.lat = lat
		this.lon = lon
	}
}

class OperatorApi {
	@warmup()
	@cors()
	@auth([ 'ADMIN', 'EMPLOYEE' ])
	@post({ location: Location, isRanger: Boolean })
	static canPickup(request, response) {
		// Business logic here
	}
}
```

Our codebase has moved almost all basic validation logic over to decorators. And because typescript supports union types, we can actually at *compile time* make sure that the values supplied to `@auth` are only those specified by the `Role` type. In other words, `@auth([ 'BLAH '])` would generate an error.

Not only that, but notice that `@post` decorator takes a dictionary as a parameter. This decorator naturally verifies that the request method is a post, and if not returns a 400. But more than that, it ensures that each of the specified parameters (`location` and `isRanger`) are both present and not undefined or null, and *also checks that they are the specified type*! This is pretty easy for something like `Boolean` which is a native Javascript type and won't be erased at runtime, but notice we can also have our own custom types work like this! The secret to this is making sure `Location` is a `class`, and not a `type` like `Role`.

Switching to typescript has saved us hours of looking to find bugs and prevented so many more! Enforcing types with clean models has been a true godsend.

### Database Guards

Fortunately or unfortunately, people often interpret NoSQL to mean "no schema", which just isn't true.

Firestore lets you input whatever you want, so if you don't use Typescript and don't have a central "data access object" / DAO layer to guard the types and values people are inserting into your database, you are in really bad shape. You'll soon find yourself in a situation where 5% of a collection has this one field, and you're not quite sure how or why it's there, but now you need to manually remove it, and now you have to write an annoying script...

The problems multiply.

To make matters worse, even if your backend has a really nice DAO layer, the whole promise of Firestore is that anyone can write to it - even the mobile client directly! How neat! In reality, it means that if there's ever a schema change or anything else, rather than letting the backend deal with that by means of something like [Avro](https://avro.apache.org) or Protobufs, the client now has to deal with it.

Firestore has somewhat decent [Security Rules](https://firebase.google.com/docs/firestore/security/rules-conditions) you can use to do data validation - so you can make *extra* sure that nobody is writing a string into that number field you've been so careful to enforce with your typescript.

The practice I found most useful was to enforce, as much as possible, that all writes go through the backend (cloud functions), and reads can go directly from the database or through the backend. Firestore provides nice persistent-connection support which allows updates to be pushed to the clients, and rather than losing out on this benefit, it occasionally did make sense for clients to direclty read from Firestore. Writes, I found, should almost never go to the DB directly.

### Cold Starts

If you know anything about serverless, you know the number one issue people face is cold starts. In this regard, AWS is significantly ahead of GCP. AWS does clever things like using a Python runtime (near instant start) for their Golang containers to get the best of both worlds, but GCP currently only supports one language (Python is in beta), and as such needs significant planning for cold starts.

Cold starts can happen anytime as your traffic spikes or whenever Google decides to use one of your machines for someone else and relocates you. Google of course provide a set of [recommendations](https://cloud.google.com/functions/docs/bestpractices/tips) to help optimize for cold starts - reducing the number of `require` statements at the top of the file being the main one.

Ultimately we ended up writing a warmer service that effectively just keeps pinging our functions to keep them warm. It doesn't guarantee a lack of cold start, but with the correct configuration it definitely helps a lot. (This was the `@warmup()` decorator that was shown above in the code snippet.)

### Data Modeling

In college, I was often taught there was one 'correct' way to model data for databases, but the more work experience I have under my belt, the more I realize 'correct' is only in relation to the particular database you are using - and more importantly, its limitations. MongoDB, notoriously, does not support multi-document atomic writes. Instead, it only executes your 'batch write' in parallel without any guarantees. (How Stripe can run a *payment* business on this database is beyond me.) SQL, famously, doesn't shard super well (which is ultimately what inspired the NoSQL revolution.)

Similarly, Firestore has two major limitations:

1) It cannot perform 'OR' queries. You must instead issue multiple queries and simply chain them together.

2) It cannot perform inequality queries on separate fields.

For example, if you have an event with a start time and an end time, you cannot perform a query like the following:

```javascript
db.collection('rentals')
	.where('startTime', '>', startTime)
	.where('endTime', '<', endTime)
	.get()
```

These are two separate fields with inequality queries. The same holds true for array lookups on separate fields, if the value you have is an array. This directly impacts your data modeling. Every event now must have a 'midpoint' time, so that you can instead re-write your query to be the following:

```javascript
db.collection('rentals')
	.where('midpointTime', '>', startTime)
	.where('midpointTime', '<', endTime)
	.get()
```

3) COUNT

Firestore does provide a way to paginate with [query cursors](https://firebase.google.com/docs/firestore/query-data/query-cursors), but if you are trying to find a way to get a count as to the number of pages, or find out whether you're on the last page, you're SOL. There is no way to find the number of entries that match a particular query

As long as you design your data models with these three limitations in mind, you should be fine.

### Pub Sub

PubSub integrates with cloud functions incredibly seamlessly. Anytime you're chaining together unrelated bits of logic, it's a sign you need to use pubsub. We solved almost all of our problems by bringing in PubSub in one way, shape, or form. Remember that unlike in the micro-service world, serverless functions need to be idempotent and not call each other directly. PubSub is the ultimate way to get around this dependency issue, and I highly recommend you use it everywhere you can.

Unlike AWS' version of PubSub, Google is still fairly far behind no scheduling events, so this is a limitation you'll have to work around.

### Firestore Permissions & UI

Although Google is rolling out new IAM roles that are a bit more granular, if you're working in an organization as opposed to side projects, the firebase permission levels are rather limiting. Giving someone 'View only' access to firestore means they can't even do basic searches like filters, and now all of a sudden people who just want to perform search filters are requiring edit access, and you're providing random endpoints just to help your ops team intelligently serach.

Also, brilliantly, the overflow menu with the three dots ir *right* next to the filter icon (which is the most clicked icon of all), and it only has one option - delete *EVERYTHING*. Love that design choice, by the way. I'm sure nobody has ever accidentally clicked on that, given the immediate proximity. Absolutely love it.

It's also nice that if you've filtered a set of documents and then click 'delete documents', it deletes *all* documents instead of the ones you've filtered for.

In other words, this is just another case of classic Google UI, and you're just going to have to deal with it. ¯\\_(ツ)_/¯

## Conclusion

Everytime I've met with someone and told them that the *entire business* runs on serverless, they're absolutely shocked. I think until now, Serverless has been a fun toy experiment that people use in their spare time for slack bots or weekend projects, but somehow the industry still believes that in order to run a 'real business', you need Kubernetes or something. I'm here to tell you that Serverless is very real, and it's the future. It's incredibly easy to onboard any new engineer, and it also forces people to think about the logical flow of the business. Writing new APIs is a blast, and we at Skip wouldn't have survived this far without cloud functions. For all my qualms with Google, they really knocked this one out of the park.