---
title: What Does a Tech Lead Do?
layout: post
date: 2018-08-03
location: Austin, Texas
description: After 3 years of being in the role for different projects, I'm retrospecting all my recent experience leading backend software engineering teams.
comments: true
tags: [leadership]
og_image: /images/tech-lead.png
keywords:
  - software tech lead definition
  - technical leadership
  - software architect
  - software team lead
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/tech-lead-small.png"
      alt="What Does a Tech Lead Do?"
      class="image-right"
      width="256"
      height="208"
      layout="fixed">
  </amp-img>
</div>

**Tech Lead** is a relatively new role in the hierarchy of software development organizations. When I heard about the role for the first time, my first thought was 

> Is a that **software architect + team lead**? 

I do not think that the definition is correct, but it's a good way of thinking about that. In the post, I  retrospect 3.5 years of my experience in the position that includes:

* leading one of the teams for **[Atlassian Stride](https://www.stride.com/){:target="_blank"}** - complete team communication solution. Within almost 2 years the team had from 5 to 10 engineers.
* leading **KPIdata** a non-profit organization that developed software for accessing the quality of higher education in [Kyiv Polytechnic Institute](https://en.wikipedia.org/wiki/Igor_Sikorsky_Kyiv_Polytechnic_Institute){:target="_blank"}.  The team was expanded to 10 core members (only 3 software engineers including myself) and eventually, 180+ individual contributors helped us to deliver the project.
* leading a team of 4 engineers (including myself) at **[Video Internet Technologies Ltd](https://www.linkedin.com/company/vit-ltd/){:target="_blank"}** for Integration of Video Management Systems (CCTV).

Note, that the same positions might have different responsibilities in different companies. 

Check out the blog post to learn about my reality of being a full-time owner of software systems. I elaborate on **pros and cons** being on a Tech Lead position.

From the practical standpoint - the list of **the most critical skills** for the position is provided at the very end of the blog.

 <!--more-->

Being a full-time owner
----

The first thing that I found on the position is that now **I'm 100% responsible** for one of the chapters of an engineering organization. The good part about that - the new chapter did not have anything in production yet. So, I did not have any legacy code from previous maintainers to support and extend. That was nice. 

However, it's not the rule, and every company is different. I think that more often you have a chance to enhance an existing software system instead create something from scratch. So, be ready to be responsible for the projects that were not started and designed by your team. 

What does it mean to be a full-time owner?

* **You receive tasks are tied up to specific business goals.** Actually, the tasks are projects. Moreover, the requirements can be partially defined. You need to go ahead and figure out all the requirements and constraints. You want to specify the desired outcome of the project as much as you can to prevent scope creep. Understanding and defining the end goals is the very first step.
* **You can do whatever is reasonable for you within your time/budget to achieve the goals.** We look into that in more details in the next section.
* **All successes (and failures) of the software that is created to achieve the goal are associated with you.** If something is broken in your system or does not work as expected - it's your responsibility and fault. In the case when the goal is overachieved - great job! Do not forget to give credit to your team for the successes though. The people deserve it.

The space for the engineering creativity
----

**Yes, you can do anything to achieve the engineering goals.** Here's the list of the things that I was able to change or implement. Note, that you should get buy-in from your team to make the changes persistent. People make software. Happy people make working software. 

* **Software development methodology**. Hardly depends on the goals of the project and deadlines. Answer the questions to define that:
  * How many days are in an iteration?
  * What's the planning process? Which tasks should be estimated? How to estimate tasks?
  * Should we accept changes in requirements or not between iterations?
  * What are the rules for the tasks of different types/priorities? Example: all bugs for the *Billing* component must be fixed ASAP regardless severity.
  * How to demo that to the rest of the organization?
* **Technical stack** for the project. It can include but not limited to programming languages, frameworks, data storages, libraries, monitoring solutions. Sometimes you have some pre-defined preset dictated by the company's policies. For our chapter the stack was the following:
  * Python 3, asynchronous programming, asyncio
  * MySQL, Elasticsearch, Redis
  * AWS (EC2, RDS, ElastiCache, S3, SQS, CloudFormation, CloudWatch)
  * DataDog, Elasticsearch/Logstash/Kibana, ElastAlert, Splunk
* **Software architecture**. You define the structural parts of the software system. You can build something new. You can reuse existing in-company or third-party services. Designing interfaces between different components is also your responsibility if you're a Tech Lead. Have fun with all that!
* **Non-functional requirements**. That's about defining the border between *good enough* and *perfect* software. I never was encouraged to make an ideal commercial solution. Usually, people *just* need a stable solution to solve their business problems. The solution should be flexible enough to let us apply new changes fast. For me, that means setting the reasonable expectation for engineers to make the business happy. Examples:
  * The component should be resilient to database restarts...
  * ...but if the connection cannot be established within 60 seconds - please, alert
* **Internal milestones**. You can set the focus for the team for different stages of the project as well as define deliverables for that. 
  * For example, the project roadmap can be optimized to have a version of the system in production ASAP to establish CI/CD pipeline as well as ensure that your ideas are principally working. 
  * Another example - you can target to make your teammates as autonomous as possible (a good idea when Y'all geographically distributed) - then you need to spend more time for planning to define independent work streams. 
* **Service Level Indicators**. As a Tech Lead, you're in charge of defining when your software provides the needed quality of service. Picking the right set of the indicators that reflect the reality of your business is vital because it sets the target for your team as well as the direction for engineering improvements. Examples from my experience:
  * **Availability.** Can the service be used?
  * **Number of processed jobs.** Do we still need the service?Hhow much useful work we're doing?
  * **Success rates for the principal components.** -Helps us to see problems on the middle level.
* **Rollout schedule**. It includes how often to deploy the software to different environments. 
  * As soon as a pull request is merged
  * OR do releases once per 4 months.
* **Communication.** How does the team communicate about the daily progress?
  * 30-minute video calls two times per day 
  * Text standup once per week *(maybe)*
* **Split of work**. How are the tasks in your Jira assigned? 
  * You assign each task to every engineer and they do not have any chance to change that without your written permission (not very good tactic)
  * Everybody can take any task regardless priority and dependencies
* **Code review policy**. Who should approve a pull request to let the creator merge it to master? Options:
  * Consensus - all concerns are answered and all default reviewers approved the changes
  * At least 2 approvals from Senior engineers should received to proceed
  * I can approve my PR and deploy after 2 hours after the last commit
* **Retrospectives.** How often to do them? My recommendation is once per 4 weeks, but I know that some teams do it every 2 weeks. Btw, how often do you do them?

I omitted some things so feel free to add your ideas as comments.


How technical is a Tech Lead?
----

My mission was to enable the team to implement the right solution to the problem.

> You do not write much code on a daily basis

Before I became a Tech Lead on the latest team, I was working more than 1.5 years on Intermediate/Senior Software Engineer positions in the same area within the same group of people. It was essential for me to gain the needed practical experience with asynchronous programming, relational and non-relational databases, instant messaging, and highload systems.

To make your project successful first of all you should **read** a lot of: 
* Code
  * Pull requests made by your team.
  * Solutions that your systems reuse.
  * Code of third-party services maintained by other teams that you need work with.
* Technical documentation
  * Description of the services that you can re-use (both in-house and third-party ones).
  * Implementation details of the solutions.
  * Known issues for them (nothing is perfect) - to understand risks and plan mitigation for them.

After the lots reading you **write** a bit:

* Engineering proposals - [DACI](https://www.atlassian.com/team-playbook/plays/daci) is a useful framework. I love it.
* After the proposals are decided - design pages.
* And in the very end - tickets for some work (my team runs on ~~caffeine~~ Jira Software).

And after the writing - you **discuss**:
* Reach agreement with your teammates regarding non-trivial tasks.
* Educate your teammates if you have a non-complete specification or did not provide all the data sources.
* Negotiate contracts with other teams.
* Demo results of your work as well as promote your solutions within the company.

At the end of the day, you might have a couple of hours to make the individual contribution. For me it was something like the following:
* Hotfixes. Needed to fix something when the world was about to explode.
* Make a proof of concept for a pull request without writing tests. After that, ask somebody from the team to turn it into the production-grade software.
* Commit database or configuration changes.
* Investigate a weird bug that can be hardly reproduced in the development environment. 
* Pull some data from metrics/logging solution to validate an idea of implementation.

I think that a Tech Lead should have solid practical software engineering experience to be able to make and support reasonable decisions. 

> On small teams (up to 3 direct reports) I think that it's still possible to make some good volume of individual contribution. 

At the moment of writing, I do not have developed my engineering leadership skill enough to be able to make sustainable individual contribution on larger teams.


Pros and Cons of being a Tech Lead
----

**Pros:**
* You become a subject matter expert in the area of your project.
* You have a complete understanding of how the software system works and how to apply changes into that with minimal risk. You can replicate it to other systems now.
* You become a good communicator because you're responsible for understanding requirements and explaining technical solutions.
* You reach some level of competency (not always very high, though) in various areas of software development:
  * **System design** - to architect your software and validate all the risks on early stages.
  * **Operations** - to keep your systems up and running.
  * **Quality engineering** - to prevent losses of your company's reputation.
  * **Engineering management** - to delegate implementation to your team or even other teams.

**Cons:**
* At the end of a workday, you often do not have a feeling of accomplishment. You have generated some new work for your team, resolved some blockers but it does not feel like real work. 
* Not enough coding on larger teams.
* You're the entry point for your team. You should be able to accept tasks from multiple sources:
  * Your teammates
  * Your management
  * Partner teams
  * Customer support team
  * Other people that have heard about your team
* Sometimes it's stressful because it's a lot of responsibility. Eventually, you should learn how to handle all that. 


I think that the position is worth trying and I'm happy that I had opportunity to serve in the position for years. I'd do it again.

TL-starter pack
----

If you're interested in a Tech Lead position and would like to prepare for that, he's the list of skills that I found valuable in the very beginning of the path:
* **Practical proficiency in the programming languages **from your stack - to be able to make good technical choices and do the code review as well. Make the proper start of the project is crucial so your coding skills can help with that dramatically to define structure and basic components.
* **Good level of skills related to data stores** - I think that in the majority of projects you deal with information read or stored from somewhere. Also, the knowledge is a perfect ground for system design competence.
* **Project management** - for organizing your work in the new multi-tasking environment as well as work of other people.
* **Communication skills** - the position is about enabling other people to do technical work.

I believe that these 4 skills are enough and the rest of the skills can be built during the project on top of them. I hope that the blog post will help to improve technical leadership in software teams.

**P.S.** In the blog, when I say "you do something" means "you're responsible for something." As a Tech Lead you can delegate some complex engineering to the experts on your team but be able to verify, approve or correct the solutions. Also, being a decision-maker does not equal to being a dictator andignoring the voices of other people.   

**P.P.S.** From my perspective, the difference between **Team Lead and Tech Lead** is in responsibilities:
* Team Lead is responsible for people, not project.
* Team Lead does People Management.
* Team Lead is not supposed to make the individual contribution.

**P.P.P.S.** Also, in my opinion, the difference between **Architect and Tech Lead**:

* Architect has more practical and diverse experience.
* Architect is needed for more extensive and more complex systems.
* Architect position is more about doing the most laborious work instead enabling the rest of the team to do all the work.