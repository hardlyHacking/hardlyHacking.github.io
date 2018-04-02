---
layout: post
title: Language Matters
date: '2016-03-02 05:43:25'
tags:
- scala
- coding
---

I consider myself to be generally language-agnostic when it comes to programming, barring any serious performance considerations. For example, articles like [this](https://blog.repustate.com/migrating-entire-api-go-python/) are entirely useless.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/angadsg">@angadsg</a> tldr: don&#39;t use an interpreted language if you&#39;re CPU bound... So tired of these false comparisons.</p>&mdash; Chander Ramesh (@chander) <a href="https://twitter.com/chander/status/642869444377620480">September 13, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Of *course* if you need a highly performant language you're going to choose something like C++/Go/Java/whatever. Just like if you need to work on iOS, you're going to choose either Objective-C or Swift. These aren't 'choices' - they're decisions already made for you.

So when people discuss language choices, generally they mean truly substitutable languages (Python vs Ruby) or languages where the tradeoffs are not immediately relevant. The former case isn't interesting. First and foremost what is your company more familiar with, and secondly for which language is it easier to hire talented engineers. Beyond that, whatever looks prettier. Who cares.

But the second case is difficult, and I think Scala is a classic example of this. Scala is an incredibly complex language that allows *many* different design and language choices, but has some nasty hidden tradeoffs.

First, let's start with the good:

a. A JVM language and Java-interop ([or so it claims](https://issues.scala-lang.org/browse/SI-4389)). For many companies, this is an absolute must. The Ops team is familiar with the GC issues already, the old libraries are written in Java, and retraining is an enormous barrier to entry.

b. Allows object oriented *and* functional programming - this is an enormous advantage in terms of flexibility for the engineers. Some problems are *way* easier to solve in a functional aspect, and others with an object oriented approach.

No, really. Take a minute to really think about it. In the roughly 20 lines of code below, I implement a fully functional language for search engines with 2 operators, AND and OR. You can nest it as much as you want, and it all works. Beautifully, concisely, and most importantly, people can reason about it! It has basic terms like <code>CAT</code> but also complex terms like <code>OMG DUDE MANY WORDS</code>.

```scala
object SearchQuery extends RegexParsers {
  def basicTerm(tweet: String): Parser[Boolean] =
    """[^()*+" \t\r\n]+""".r ^^ { tweet.contains(_) }
  def quotedTerm(tweet: String): Parser[Boolean] =
    """"(.*)"""".r ^^ { case str => tweet.contains(str.drop(1).dropRight(1)) }
  def term(tweet: String): Parser[Boolean] = basicTerm(tweet) | quotedTerm(tweet)

  val and: Parser[String] = "\\*".r
  val or: Parser[String] = "\\+".r
  val ops: Parser[String] = and | or

  def factor(tweet: String): Parser[Boolean] = term(tweet) | "(" ~> expr(tweet) <~ ")"
  def expr(tweet: String): Parser[Boolean] = ops ~ rep(factor(tweet)) ^^ {
    case op ~ list => op match {
      case "+" => list.foldLeft(false)(_ || _)
      case "*" => list.foldLeft(true)(_ && _)
    }
  }

  def apply(query: String, tweet: String): Try[Boolean] =
    parseAll(factor(tweet.toLowerCase), query.toLowerCase) match {
      case Success(r, _) => Return(r)
      case f: NoSuccess => Throw(new Exception(f.msg))
    }

  def contains(query: String, tweet: String): Boolean = 
    apply(query, tweet) match {
      case Return(r) => r
      case _ => false
    }
}
```

It's absolutely beautiful!

c. Seems ideal for API design; in many ways, your server [is a function](http://monkey.org/~marius/funsrv.pdf), and a goal to minimize side effects is almost exactly what Scala is designed for.

d. But I think most of all, Java is *ugly*. Everyone who works with Java agrees that it's verbose, it's ugly, it's disgusting. And there's a natural, human inclination to think that [new is always better](https://www.youtube.com/watch?v=x7_KFazcb3w). And Scala is pretty.

So what are the tradeoffs?

Well, all this awesome flexibility comes at a terrible tradeoff of [horrible complexity](https://www.youtube.com/watch?v=TS1lpKBMkgg) - and complexity has very real consequences. No, seriously - even the [basic collections](https://www.youtube.com/watch?v=uiJycy6dFSQ) don't seem to make sense with Scala's own internal type system! So you get insane situations, such as the following:

```scala
scala> val x1: Float = Long.MaxValue
x1: Float = 9.223372E18

scala> val x2: Float = Long.MaxValue - Int.MaxValue
x2: Float = 9.223372E18

scala> println(x1 == x2)
true
```

This sort of complexity rears its ugly head *everywhere*. The Scala compiler has [30 some odd phases](http://stackoverflow.com/questions/4527902/what-is-the-order-of-the-scala-compiler-phases). How does one optimize this madness? The Scala compiler has over 200k LOC, and *to this day*, reflection is not fail-safe! Why? Because it's So. Darn. Complicated. What does this mean?

It means a clean compile of your code at Twitter takes ~40 minutes.

Think about that. 40 minutes between every compile / deploy to staging / check if the bug was fixed!? *40 minutes*. It means that your company has to fundamentally prioritize your build/deploy teams because nothing out of the box works! You're sacrificing developers so that your developers can... well, develop.

I hope your company aggressively caches various builds and sets up an awesome team to help speed up compilation - oh, wait, Typesafe owns the Scala compiler, so the scala-zinc phase, the thing that takes the longest, is something that you have *no control over*.

Of course, given enough time (and enough developers), a big company like Twitter can make anything work. But one has to wonder whether the tradeoff was worth it - and *most* importantly, what the framework was for making such a decision.

Let me be clear - Scala isn't the only language with this problem; it's just the one with which I am most familiar. But the industry is riddled with these questions - particularly in smaller companies that simply *cannot afford* to make a mistake in this area. Ultimately, we need a decision matrix with factors and weights for importance to make this decision.

Get rid of your personal feelings; get rid of what you feel is 'pretty' or 'readable'.

There are several factors one needs to consider:

1. How much will the best alternative improve in 1 year? 5 years?
2. What's the build / compile / deploy cost of your language?
3. What's the performance of your language per whatever load metric makes sense in your context?
4. What's the popularity / ease of hiring / ease of learning / ease of coding in your language?

Number one is the single most important factor to consider. Certainly Scala was significantly better than Java at the time of its conception, but once Java 8 came out, we saw [LinkedIn get rid of Scala](https://www.quora.com/Is-LinkedIn-getting-rid-of-Scala) in favor of Java. Indeed, Google did a similar analysis and reaffirmed its commitment to Java. And the future of TypeSafe [doesn't look too bright](https://www.linkedin.com/pulse/scala-way-out-owen-rubel).

This happens over and over. Coffeescript was the new hip thing until ES6 came out. Twitter's internal Manhattan project was *way* better than Cassandra 0.7 - until four years later when the open source community caught up, and now Cassandra 3.3 is *way* faster than Twitter's home-grown solution. The general pattern is that, for a short-term gain (and a *huge* investment with developer-time), you've temporarily beaten the inevitable industry solution.  In other words, it's not enough to talk about how you're better than the alternative *today*. The question is **how you will continue to be better in the years to come, and how many resources that will take?**

Too many people ignore the building, compiling, and deploying cost of programming languages. In Scala's case, it was compile time. In Haskell's case, it's often the deploying. Whatever environment you're working in, you simply have to consider the entire lifetime of your code, from inception to deployment to retirement. I've never felt more jealous than when my friend at Docker told me that, thanks to Go, they can compile their entire code in under ten minutes. I couldn't even non-clean compile my microservice in under ten minutes.

The third criterion harkens back to the initial example. Comparing an interpreted language to a compiled one like Go in CPU-bound tasks is silly. Whatever metric-load your situation demands, make sure you use that to compare.

And of course, for the long-livedness of your architecture and your core services, for the love of God, please use something that's tried and true and will always be around. Regardless of how much we tout Scala at Twitter, when it comes down to it, nearly all of our Platform core services are written in Java. I'm not sure why this doesn't apply for the rest of our services - apparently, only our core services have to be performant, readable, and compile quickly. <code>:'(</code>

tldr; "This language is ugly" is not a reason to base your architecture on something. Language doesn't matter to a project. But it does matter to a company. And newer is *not* usually better.