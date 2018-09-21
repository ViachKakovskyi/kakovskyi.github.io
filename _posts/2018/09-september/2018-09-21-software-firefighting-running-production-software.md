---
title: "Running Production Systems: Level 1, Software Firefighting"
layout: post
date: 2018-09-21
location: Austin, Texas
description: You Build It, You Run It. The slogan spreads all around the world across software engineering teams. There are different levels of operational maturity, and I encourage you to find the one that fits your team. This week we review Level 1 - Software Firefighting.
tags: [harmful advice, development process, reliability]
og_image: /images/this-is-fine.jpg
keywords:
  - running software in production
  - you build it you run it
  - how to build reliable software
  - how to monitor software in production
  - software firefighting
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/this-is-fine.jpg"
      alt="Running Software in Production. Levels of Maturity"
      class="image-right"
      width="300"
      height="300"
      layout="fixed">
  </amp-img>
</div>

**You Build It, You Run It.** The slogan spreads all around the world across software engineering teams. It's working great - the successful teams care not only about writing good code, but also how the code is serving the end-users in the production environment.  

For [Highload projects](http://allyouneedisbackend.com/blog/2017/08/30/what-is-highload/){:target="_blank"} running software turned into a separate discipline called **Site Reliablity Engineering**. As one my fellow (former DevOps Engineer, SRE now) told me:

> #### SRE is the next level of DevOps

I think that engineering teams should know how the software will be running in production starting at the very beginning of the project. It includes the knowledge about infrastructure, data storages, monitoring, and deployment pipeline. Good, if you know the budget for all the things.

In the *series of blog posts*, we focus on the approaches that I've seen during my career, starting from the simplest to the most sophisticated one.

Let's check out the very first level that I called **Software Firefighting**.

<!--more-->

Why put firefighting foam on your code
---
**Software Firefighting** - is the spot where engineers usually start. You do not need to have any experience to begin. Doing that actually can help you to grow... until some point.

You write some code, push it to production, and hope that everything works fine. Yes, you do not have a lot of information on how the system behaves and how healthy is that.

This tactic is OK for the cases when it's not a big deal if your service does not operate properly:
* Pet projects
* Students' projects
* Hackathons (I've never had proper monitoring during a hackathon, had you?) 
* Prototypes/demo projects

But I used to work in the environment when business-related applications do not have any telemetry. 
> #### My boss called me at 11 PM to tell that the piece of ~~sh*t~~ fantastic software is not working, and the next morning we're demoing the system for the customer that should pay for that. 

Trust me; it's not funny at all. After such calls, you connect to the box that runs the software via SSH. You try to understand what's going on. In Software Firefighting mode you try to reproduce the issue in production to see in logs something useful. Another option if your logs are not very verbose or you want to jump into lower level - attach a debugger to a running process. 

If you stick with the Software Firefighting approach for running production software, I see a high probability for you to be working late at night. Very late at night... Might be OK for the fans of nightly coding. Not sure if you be paid for the overtime though.

> #### The vendor from another continent dialed me. They asked to expedite issues with our software that are happening right away during the exhibition. It happened to me at 3 AM. The exciting experience that I would like to avoid in the future.

When you find the cause - you experiment on the live instance try to patch the code, eventually create a PR that starts with the word `hotfix` and deploy this straight to production without proper peer reviews. Your team will learn later about that... hopefully not from a new incident.

Ultimate Software Firefighting workflow
---

Well, I warned you that it's not suitable for business applications. Now I will share my thoughts how to do it.
1. Identify the problem.
  * Connect to the production environment.
  * Collect live stats (in the next section will discuss how to get trained in that area).
2. Reproduce the problem.
  * Localize - find the place where it's happening. The smaller is the localized area - the better is it for you: service, module, class, method, code statement, variable...
  * Repeat the harmful action to verify if that the right place (only if that's safe from the business perspective).
3. Prepare the hotfix.
   1. Find similar problems in your incidents registry/closed Jira tickets or on Stack Overflow.
   2. Make the change locally.
   3. Test it without touching production.
   4. If you're sure about the change - move it to production.
   5. Verify that the problem is solved (and the new issues were not created).
4. Write a blog for your team to share your learnings from the firefighting session.

Train the firefighters
---
Troubleshooting in production is a great skill. I wish I had it on the higher level then it's now (but I do not want any business to pay for that). Here're the levels of troubleshooting, ordered from the easiest to more complex things:

1. **Check the state of the box that is running the application (healthy or not)**.
   * **[top](https://linux.die.net/man/1/top)**{:target="_blank"} - the command collects resource usage statistics on your machine and provides the dynamic view the utilization. That's the easiest thing that you can do to gain some situational awareness. Links for learning:
     * [Linux Load Averages: Solving the Mystery](http://www.brendangregg.com/blog/2017-08-08/linux-load-averages.html){:target="_blank"}
     * [Understanding the output of TOP command](https://gtacknowledge.extremenetworks.com/articles/How_To/Understanding-the-output-of-the-TOP-command){:target="_blank"}
     * [15 Practical Linux Top Command Examples](https://www.thegeekstuff.com/2010/01/15-practical-unix-linux-top-command-examples/){:target="_blank"}

2. **Check the statements that the software is logging**. The logs for your service, web server, load balancer - all the things. `grep` and `tail` are your best friends that here.

3. **Investigate the suspicious running process(es) without restarting.** Since it's not always straightforward how to reproduce bugs in production, I was thrilled when I learned that we could connect to the code that already serves our customers. It gives the ability to have a much way deeper look into details. Some tools that can give you a clue:

   - **[gdb](https://www.gnu.org/software/gdb/)** - is the GNU debugger that allows you to set breakpoints and temporarily stop the execution of a process to see values of variables in real time. The more you understand source code of the program the easier it would find the error. When you need to investigate a crash you can set a breakpoint just before the program crashes. Some tips:

     - You can [see waiting threads using gdb](https://benbernardblog.com/my-startling-encounter-with-python-debuggers/){:target="_blank"} 
     - You can [extend gdb using Python](https://sourceware.org/gdb/onlinedocs/gdb/Python.html){:target="_blank"} (you can also go that with GoLang AFAIK)

   > #### The tool helped me to figure out an issue with regular expressions that caused a major incident in a large cloud system.

   **gdb** gives you more insights that you have looking only in logs. You can find many blogs on how to use the debugger for your technology stack. I'd like to share the link to blog post about [Debugging of CPython processes with gdb](http://podoliaka.org/2016/04/10/debugging-cpython-gdb/){:target="_blank"} by [Roman Podoliaka](https://twitter.com/rpodoliaka). For the backend applications written in Python I can be more convenient to use [pdb]( https://docs.python.org/3/library/pdb.html){:target="_blank"}. Here's [the great tutorial](https://github.com/spiside/pdb-tutorial){:target="_blank"} how to start using the tooling. 

4. **Trace all the things on the box (network traffic, library calls, system calls)**. If nothing of above helps - look into the universe of tools and approaches for introspecting your software. Check out the links for learning:

   * [Choosing a Linux Tracer (2015)](http://www.brendangregg.com/blog/2015-07-08/choosing-a-linux-tracer.html){:target="_blank"}
   * [Linux Performance](http://www.brendangregg.com/linuxperf.html){:target="_blank"}

Also, we have a couple of tools suggested by subscribers ([Carlos Neira](https://twitter.com/CarlosN26157061){:target="_blank"} and [Amie Wang)](https://twitter.com/Amie42){:target="_blank"} from [the related Twitter thread](https://twitter.com/BackendAndBBQ/status/1026138692514205699){:target="_blank"}:

* [BPF Compiler Collection - Tools](https://github.com/iovisor/bcc){:target="_blank"}
* [kgdb](https://www.kernel.org/doc/html/v4.15/dev-tools/kgdb.html){:target="_blank"} 
* [mdb](https://docs.oracle.com/cd/E18752_01/html/816-5041/intro-1.html){:target="_blank"} for Solaris OS
* [Valgrind](http://valgrind.org/){:target="_blank"}

If you're interested especially in Performance Optimization, I extremely recommend you to watch the videos (90 mins) about **Linux Performance Tools** by [Brendan Gregg](http://www.brendangregg.com/){:target="_blank"} - [Part 1](https://www.youtube.com/watch?v=FJW8nGV4jxY){:target="_blank"} and [Part 2](https://www.youtube.com/watch?v=zrr2nUln9Kk){:target="_blank"}. He explains [the performances optimization methodologies](http://www.brendangregg.com/methodology.html){:target="_blank"} and provides a good number of practical examples. And [read his blog](http://www.brendangregg.com/overview.html){:target="_blank"}. It's a lot of knowledge.

During learning the topic, I also found [the slides](http://lethargy.org/~jesus/misc/production-troubleshooting.pdf ){:target="_blank"} from [Theo Schlossnagle](https://lethargy.org/~jesus/page/about/){:target="_blank"} interesting.

Pros and Cons
---
Let's analyze the benefits of the cowboy style of running production software.

**Pros:**

* **Heroism.** You feel like a hero rescuing your business from disasters.
* **Cheap.** No investments in your infrastructure are needed.

**Cons:**

* **Affects the reputation of your business.**  You're aware only when customers/business owners find that service does not work. Example: you learn from Twitter feed that your service does not work for end-users and they're moving to a competitor. It sucks.
* **Requires engineering team to be trained.** 
* **Time-consuming.** You need to reproduce the issue in prod to gather telemetry right on the box.
* **Not accountable.** Leads to running hotfixes in production that might not exist in your repository. And the other engineers on your team might learn nothing how to fix such issues.
* **Stressful.** Dangerous to your mental and physical health. As well as your personal life.
* **Non-cooperative.** It's hard to handover work if you need to step-out.

**Again, I do not recommend running business software applications in the Firefighting mode.** 

We can remove some disadvantages of the approach. To achieve that we need to grow and reach the next level of maturity. The second blog post in the series will be published soon.

**Well, what's your favorite debugging/performance tool?**

**P.S.** The blog post started as [the Twitter thread](https://twitter.com/BackendAndBBQ/status/1026138692514205699){:target="_blank"}.  You can subscribe to my Twitter account or blog to do not miss the next knowledge sharing session about backend software engineering.