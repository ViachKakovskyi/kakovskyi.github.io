---
title: "What Is a Highload Project?"
layout: post
date: 2017-08-30
location: Austin, Texas
description: |
 One of the buzziest words in software engineering is explained. Yes, it's about HighLoad.
comments: true
tags: [highload]
og_image: /images/what-is-highload-new.png
keywords:
  - high load project explained
  - highload software projects
  - what is highload
  - what project is highload
  - is my project highload?
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/what-is-highload-new.png"
      alt="What Is a HighLoad Project?"
      class="image-right"
      width="270"
      height="302"
      layout="fixed">
  </amp-img>
</div>

Highload. It was the main buzzword for me 5 or 6 years ago. Since [The Social Network](https://en.wikipedia.org/wiki/The_Social_Network){:target="_blank"} movie was released, I wanted to develop such kind of software.

The domain area did not matter for me then: dating services for founders of dating services, illegal online casinos or websites which stream questionable video content - everything would be okay. I wanted to be a part of the team which solves complex engineering problems in scale and delivers product to *many thousands and millions of users simultaneously*.

I had read dozens of definitions on the Internet from different sources. But I did not understand what does highload mean.  And now after years of development of various highload projects I created **my very own definition of highload**.

<!--more-->

----

What The Internet says on Highload
-----

Let me share with you the aggregation of my findings from different sources:

> Highload begins when one physical server becomes unable to handle data processing.

It sounds reasonable, does not it? But I cannot agree with the definition because it does not count software for the systems which cannot scale at all. Like embedded ones.  

> If a single instance serves 10,000 connections simultaneously - it's highload.

The statement is interesting and refers to the [C10K](http://www.kegel.com/c10k.html){:target="_blank"} problem. But I think that it wrongfully excludes the systems which handle fewer connections.

> Your project is highload if it processes 100+ dynamic requests per second.

It does not sound _serious enough_ if we think about regular HTTP requests when an application flips a bit in a database. But if processing on backend requires a lot of CPU work - why not? Anyway, let's skip this one because it's not universal.

> Highload is about serving thousands and millions of users simultaneously.

This opinion is prevalent. Not so many projects can boast such numbers. I think that having the tons of customers is not required to be a highload system. But it's definitely enough.

> Highload starts when regular and obvious solutions stop working and you need to make some tricks to handle the traffic.

I like this one! A kind of agree with that. Found this too late :).

> If your infrastructure cannot consume incoming streams of data and requires horizontal scaling - welcome to the Highload club.

Horizontal scaling and highload are very often together. But it's not a rule for me.

> Highload websites are the same as usual ones, but they have a very large audience and use a lot of optimizations to handle the load.

It's not only about websites. And you do not need to have the _large audience_. From another side the idea is good.

> If you use one very fat machine, your project is a rather a highload one.

Yes, it can be true. And it's also not a mandatory feature.

> Usage of Lambda Architecture and Kafka makes the system highload.

We can rephrase it to the more general: *Usage of technology X or architectural pattern Y makes your project highload*. I strongly disagree with that. A student can do a personal project which never hit a real production except testing by his/her friends with non-real-world samples of data. We need to put some stress on the system from real life customers/dataset to call it *highload*.

> If you're deployed on AWS, IBM Bluemix, Azure, or Google Cloud Platform, then you're maintaining a highload service.

This definition does not make a lot of sense for me. What about on-premise solutions? Does it mean that they cannot be in the Highload club? Nope. By the way, cloud computing offers a lot of services to speed up development and make scalability a bit easier.  

Well, Viach, what's Highload?
------

As you probably noted, all the listed definitions and tons of others did not satisfy me completely. I envisioned all the experience which I had, stuff which I read to make this statement.

> ## What is a highload project?
> #### A project when an inefficient solution or a tiny bug has a huge impact on your business. The mistake leads to increase of cost **$$$** or loss of company's reputation.
>
> #### From the engineering perspective, a lack of resources causes performance degradation.

I like this definition more than others because:
* There's nothing about numbers: servers, connections, requests, users. The features can vary from project to project.
* We do not specify technologies, vendors, and patterns.
* It's easy to define for an engineer if a project is highload at this stage. If you already cannot afford to make rough decisions without impact to your business - you're in the safe spot. Yet. Otherwise - please, be responsible and careful.
* It provides a better explanation to business stakeholders why your team should build one solution over another. Or get buy-in for providing some additional resource. Remember, that we all make software to solve some real-world problem.

Does the definition work for every case in the world? I'm not sure. But it helps me to define when we need to invest more time into optimizations and when we should avoid that.

Very likely, that the backend software which you make in 2017 consist of several components. Some parts of the system might require more focused efforts because of nature of highload which I mentioned. Another part might be all right with using trivial solutions copy-pasted from a tutorial.

Your goal as an engineer is to find the trade-off and figure this out in the best way for your business. It does not matter if you define the project as highload or not ;).

**Do you work on a highload project?**

**What's your definition of highload?**

-----

P.S. I made a few talks **about highload** some time ago, check them if you're interested to learn more on the topic:
 * PyCon Ukraine 2016: [Maintaining a high load Python project for newcomers](/talks/#pycon-ukraine-2016){:target="_blank"}
 * PyCon Poland 2016: [Maintaining a high load Python project: typical mistakes ](/talks/#pycon-poland-2016){:target="_blank"}
