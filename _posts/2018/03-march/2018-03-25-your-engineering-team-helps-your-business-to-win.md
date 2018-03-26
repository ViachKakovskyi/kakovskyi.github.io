---
title: Does Your Engineering Team Help Your Business To Win?
layout: post
date: 2018-03-25
location: Austin, Texas
description: Thoughts, notes, and action items after reading "The Phoenix Project" book through the prism of my experience. Reading the blogpost, you will learn why I recommend reading the book. The post targets engineers that are managing commercial software projects or would like to start doing that.
comments: true
tags: [leadership, devops book club, reading notes]
og_image: /images/the-phoenix-project-small.png
keywords:
  - the Phoenix project reading notes
  - how to sell your code and make money
  - DevOps book club
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/the-phoenix-project-small.png"
      alt="Does Your Engineering Team Help Your Business To Win?"
      class="image-right"
      width="256"
      height="208"
      layout="fixed">
  </amp-img>
</div>

Yey, *DevOps Book* club was started in the office. I joined since I love DevOps, increasing the productivity of my team, and, of course reading books. I even did not imagine how useful it can be for solving day-to-day organization challenges with my team and my coaching as a tech lead.

The first book for the club was **[The Phoenix Project](https://itrevolution.com/book/the-phoenix-project/){:target="_blank"}** written by Gene Kim, Kevin Behr, and George Spafford. People call the genre as *business fiction* - it's a story about an IT manager (ex-marine) that was unexpectedly promoted to VP of IT Operations.

In the blog, you can see my thoughts and notes on reading the book **through the prism of my experience working on a team and leading teams**. Actionable items are provided as usual.

Note, that we won't be covering the plot of the novel, if you're interested in that - read the book.

 <!--more-->

High-level takeaways
----

* **Learn why your business needs you to make every single bit of software**. If you work on a commercial project, you should know why you're paid. And how the cash for your paycheck is generated. Each code commit should provide additional value to your company.
Examples:
  * A new feature that attracts new users or makes current users more satisfied with the product. Or even causes customers to buy the premium version of the product.

  * A bugfix that makes existing customers happier and retains them with your product instead of making them think about solutions made by competitors.

  * An improvement that makes engineering/product/support teams more productive to release their time to do other beneficial work

  * Maintenance that prevents future issues or incidents that can lead to loss of trust of your customers. Also, incidents eat your time that should be dedicated to other work.

  I think that in commercial development, each software system is related to some business goal. If you do not know the goal that your team is achieving - ask your management. If there is no objective - question, what's the need for the company to pay you for work?


* **Breakdown business OKRs or KPIs into engineering deliverables**.
  * Need more users? Learn why you're not gaining MAU and find how your code can add them.

  * Need the product to have higher reliability? Invest into that by setting up a special team to do preventive maintenance.

  * Need to have more revenue? Pay attention to make paid features more attractive. Make sure that the things that you do and your team does help the organization to achieve at least one of the goals. Otherwise, it's waste of your talent and company's money.

* **Identify the types of work that your team is doing**. According to the book, the four types of efforts exist:

  1. *Business Projects* - that's what you're asked to do by product managers/project sponsors. Goals of such project are tight to business objectives. Execution such projects increase MAU, customers' satisfaction, or revenue.

  2. *Internal Projects* - that's what you need to keep achieving your business goals. Engineering incentives from your team or asks from external teams fall into the category. Results of finishing such projects are not so visible out of the groups of people involved into that. But when such projects are skipped or executed poorly - it impacts business functions.

  3. *Changes* include actual deployment of deliverables made during the two types of projects listed above. Also, the category covers all small housekeeping work.

  4. *Unplanned Work* - dealing with incidents, and emergencies. You probably do not have time in your work schedule allocated for that. Doing that distracts you from doing other types of work. That sucks.

* **Implement change management process** within your organization. Changes in code or infrastructure that are done by one team can affect another team. You do not want to be surprised when they "upgrade" the version of the company-wide database to the one that does not have your favorite deprecated function. More real case - database schema changes that can be hardly reverted. I know that this can be hard, but you (or your management) should:
  * Build product roadmap for each team at least for one quarter.
  * Make it aligned across various teams.
  * Communicate about all backward-incompatible changes ahead of time if you have any.


  To succeed in leading your team, you should think outside your team/department or event product. Think, how the significant changes that you're going to bring affect the company as its customers as a whole. Always evaluate the risky changes.

* **Define your development process and find the bottleneck**. The process might vary from team to team even within one organization. The steps might include the following actions (the list is *very* simplified):

  1. Evaluate customers' feedback. If your customers vote in public Jira for some functionality or open support tickets - you're lucky. Use that as the input.

  2. Identify the real need behind the request to define a feature. Define functional requirements for the software. Prioritize the feature and put it into the product roadmap.

  3. Allocate engineering resources within the organization to implement the functionality. Define non-functional requirements.

  4. Design how the feature should be implemented. Make work breakdown structure and plan the execution. Communicate with external teams if any assistance is needed.

  5. Write code. Cover the code with tests. Perform peer-to-peer review. Fix comments. Deploy to staging. Perform testing in staging. Find bugs. Fix bugs. Deploy the code to production.

  6. Enable the feature for the customers. Receive customers' feedback. **GOTO p.1** :)

  Each of the steps involves different skills and roles - from support engineers and product managers to software and quality engineers.
**Your goal is to find the constraint - the slowest/busiest chain link and make it faster**.


  According to the Eliyahu M. Goldratt’s “Theory of Constraints.”:

> ####  Any improvements made anywhere besides the bottleneck are an illusion.

  Only in that case you will see the improvement in feature delivery and be achieving business goals as a result.

* **Set limits for Work-In-Progress**. Having the number of tasks in progress higher than your throughput means the following:
  * Your organization already paid for something to be done but your customers do not get the value from it.

  * Since the work in-queue is waiting for resources that means the money is not used to maximize value for the organization.

  On my team, we limit the number of In Progress tickets to the number of engineers. It's very unlikely that one engineer works on two tasks at the same time. In that case, one of the tickets is probably blocked or waiting on somebody else to provide input.
You should focus on finishing the in-progress work instead of starting work on new tasks.

* **Standardize the work that your team is doing**. Hardly ever your squad faces with unique tasks. Common tasks can be:

  * Perform database schema change.
  * Change infrastructure configuration.
  * Add more verbose logging; tracking more metrics and dashboards for them.
  * Add an API endpoint.
  * Add usage of a new API endpoint provided by an external team.
  * Profile and optimize some piece of software.
  * Change business rules for data transformation.
  * Refactor a module for better maintainability.
  * Investigate customers' request.

  The idea is to collect historical data about the way which the tasks are resolved as well as the time needed to implement the changes.

  The information should help you to achieve two objectives: first, train new team members - they can look how similar issues were resolved; second, provide estimates for your business owners.

* **Do not raise critical players**. If only one person on a team can perform some tasks, it makes the person extremely busy. And the team becomes exceptionally dependent on the engineer.

  Eventually, jobs are waiting for the person to be free. The engineer becomes constraints for your project. To have sustainable development process, you want to have *at least two persons* that can do some task.

  To eliminate bus factor, initiate internal knowledge sharing and invest in automation and documentation for the things that cannot be described as code. It helps you to raise team players. The excuse *"it's easier for me to do that than expain"* should never be approved.

* **Track all work requests that come to your team**. Product managers and support engineers will be distracting your team. From achieving the goals that they defined for you. Yes, it sounds like a paradox. But it happens. I think that the reason of that: it's hard to evaluate all customers' needs and prioritize them.

* **Ideally, you should budget some time for urgent/unplanned work.** Each request should have a Jira ticket. Asks from external engineering teams should be tracked and prioritized as well. For example, if your service exposes a private API that is used by ten other engineering teams - be ready that some of them will ask you for some customization of non-trivial support. And vice-versa - your vendors inside your company can change the rules of the game because of their needs.

* **Enable your business to make experiments**. Engineering teams should provide the ability to verify product assumptions with minimal investment into implementation or without coding at all.

  I think that in the quarter our team succeeds in the field: some business was able to do some experiments without distracting the team from achieving other commitments. The way how to do that was not clear to me at the beginning of the journey, but after reading the book and having actual results, I see the full picture.

* **Make your business accept risk when they do not give you resources/time/budget**. We as engineers can suggest the priority for some maintenance tasks and preventive actions. Good, if we can provide insights about possible customers' impact. It's *always* not enough time to fix all bugs and build all the features. Your business owners should understand trade-off and decide your priorities.

* **Freeze low-priority work**. It's better to have your team working on a couple of in-flight projects and accomplish them in-time rather than have Work-In-Progress that already consumes resources and does not provide value for the business, customers or your team yet.

* **Consider using cloud providers.** They offer opportunities to think less and lend resources instead of buying them or mastering more complex/efficient algorithms. If processing of some background job takes enormous unacceptable time with your current codebase/ infrastructure  - consider parallelizing that with enabling additional computational resources only for the time of the job.

* **Consider outsourcing.** Some parts of your business or legacy applications can be given to external vendors. It can reduce cost and release the smartest brains that are on your team. But make sure that the contract includes not only maintenance of the system but also the implementation of the changes needed to support your possible business initiatives. Also, make sure that the outsourcing team is capable of doing the required changes timely.

Other engineering tips
---
* **Automate installation/provisioning of the environments needed for development, quality assurance, staging, and production.** Keep the environment as much close to each other as you can - same versions of OS, databases, library. You should be able to access them fast: keep them provisioned and pay for that or make the provisioning fast. Manual instructions should die, and manual changes should never be applied.

  Remove humans from the deployment process. Maximum involvement should be clicking the *Deploy Now* button. Set up of development environment for new teammates should be done within a day or so.

* **Setup delivery pipeline and measure the throughput**. Classically, it includes writing code and deploying code to production to deliver value to the end-customers. In my opinion, it also includes identifying a need (business or engineering one) and prioritizing the needing/scheduling the work.

* **Document (even better, automate!) "magic fixes" for all incidents**. You need to be able to replicate them if the issue occurs again. Keep them in your projects' knowledge base. You cannot rely on the hope that the engineer that solved the problem the last time will always be available to assist. That's it, changes in the systems that you own should be transparent and repeatable.

* **Proactively find all fragile parts of your software**. If you work on the system that was developed before you joined the team - be ready for surprises. Things can break where you do not expect that. Besides codebase and project documentation (if your team has good enough documentation) your sources to learn that can be: results of load testing, metrics, and logs from production, registry of closed bugs, customer support tickets.

* **Stabilize infrastructure to be focused on development, not firefighting.** It's hard to make reasonable estimates and do not work overtime when you need not only to develop new features but also keep existing buggy software up and running. I will post a separate blog on the topic. Stay tuned.

* **Include slack time into your business commitments**. If engineers on your team are 100% loaded according to your plan that means any unplanned work should wait for in-queue (this is bad) or the commitments won't be met. Having some idle time for your engineers is fine since you cannot predict actual time to accomplish work as well as changes in requirements.

* **Avoid handoff of tasks between engineers and cross-teams.** Context switch kills productivity. Having more than one responsible person enforces corporate ping-pong and makes harder to get things done.

* **Measure how often your code CAN be deployed to production**. Do you know how many deployments per day your business needs? How many of them can you do without affecting your routine? You would like to know the answers at least for the case when an incident occurs, and you need to push the hotfix to prevent loss of the company's reputation.

* **Make all code changes accountable and authorized**. As well as infrastructure changes they should go through version control system, peer-to-peer review process and *sometimes* approved by business/budget owners or external teams.

* **Move your working code to production ASAP**. Until the code is in production and is enabled for customers - no value is generated from doing product research, creating Jira tickets, design meetings, writing code, and reviewing pull requests.

* **Make faster releases** and do that in small batches. For me, the ages when we give our customers a new version of backend software that *runs on our infrastructure* once per month are over. Every merged pull request should be deployed individually (and rolled back). In that case, you can observe how the change affects your system and find failures fast.

* **Prepare rollback strategy for deployment of all large/risky changes.** Examples: altering database tables with dozens of records, extreme refactoring, data migrations, switching vendors. If you think that your testing is not enough (or it's expensive to cover all needed cases) - I would invest into that.

* **Know about your incidents before your customers or business find that.** First of all, it gives you more time to investigate and fix the issue. Secondary - timely updated status page is the face of your team. It's just caring about feelings of your customers.

* **Build a passionate team that is OK to work late hours and weekends to rescue the business when it's really needed**. It should be compensated somehow eventually including additional days off to recover and spend time with family or friends. You also can setup on-call rotation to have somebody on duty 24/7 be ready to fix any problems.

I'm happy that **The Phoenix Project** book was selected for the DevOps book club. Reading the book, discussions with other engineers and retrospective look back helps me to define the next steps to improve development process in our backend engineering team.

**What's favorite book about DevOps?**
