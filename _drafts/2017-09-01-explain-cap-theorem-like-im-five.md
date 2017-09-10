---
title: "Explain CAP theorem like I'm ten. With street soccer"
layout: post
date: 2017-11-01
location: Austin, Texas
description: |
 Be prepared when your kids ask you about Consistency, Availability or Partition tolerance in distributed systems.
comments: true
tags: [distributed]
og_image: /images/explain-cap-theorem-like-im-five.jpg
keywords:
  - cap theorem explained
  - consistency availability partition tolerance
  - distributed systems for dummies
  - cap theorem distributed systems
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/explain-cap-theorem-like-im-five.jpg"
      alt="Explain CAP theorem like I'm ten"
      class="image-right"
      width="250"
      height="345"
      layout="fixed">
  </amp-img>
</div>

When I was ten almost all boys in our area played [street soccer](https://en.wikipedia.org/wiki/Street_football){:target="_blank"}. I was a part of the amateur soccer team representing our street. It was a great team - a group of close friends. And it might be the main reason why we lost majority of the games - looking back I can tell that we were not enough strict to each other.

We had an amateur competition between street teams in our district. With prizes and participation fee about $1 per a player. It was a big deal to win the tournament. We had them a few times per year during school holidays. And between the tournaments we played dozens of friendly matches with other teams to reach a good shape as team for the important games.

Why I tell y'all about this stuff in my engineering blog?

Well, I'm going to use **the process of finding a soccer team for a friendly match as the example to explain the [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem){:target="_blank"}**. If you never played soccer - it's fine, substitute soccer with any other team sport. The story is more about communication.

*The topic is inspired by the set of blog posts from [Explain Like I'm five](https://dev.to/t/explainlikeimfive){:target="_blank"} category, but I did not find a real-world analogy for 5-years-old kid.*  

<!--more-->

----

Starting from scratch
-----

You're ten years old. You go to school. Almost do not skip classes.
You have 8 friends which love to play soccer. To play a game you need to find at least 5 members in total.

All your friends live on the same street with you. It's from 2 to 10 minutes between your home and a house of your friend. Everyday you meet during a school break to discuss next *potential* games.

Why *potential* games?

Because kids do not own their time - parents can urgently take them to relatives in a village to help with harvesting potatoes: it was with my friends in real life and they missed some soccer games even during a tournament.

So, after your *daily standup* everyone has a vision if he/she can potentially play a game at some time today. You even can already have a *potential* agreement with a team (from another street), which is also a team of kids which do not own their time :). Overall, 8 teams are available in your district excluding your one.

You usually use a landline phone to finally negotiate time and place for playing soccer (in my childhood kids did not own mobile phones).

Mostly, a representative of **Real Bits** team dials to a representative of **Git United** team to confirm/cancel a game.
1. After that the representative of **Git United** polls the team via landline phone to get the needed quorum for the match.
2. If the quorum of 5 players is received - the representative responds to his vis-à-vis with team's decision. And the game begins.

Note, that any team member can be such representative. There is not single responsible person like a team captain.

Each team is interested to play as more friendly matches as they can to prepare to the tournament really well.
Also, there is an agreement that one team should provide answer to the other time in 10 minutes or earlier. In other case the game is considered as canceled.


When the problems start: network partition
-------

All the communications between teams to negotiate a match is done via landline phone. As well the communications inside your team.

Imagine, that at school your team **FC Recursion** reached a preliminary agreement for today's friendly match with **Dynamo FC** team. After school your team members went home to make homework and prepare for the game which should start at 4 pm. Waiting for the confirmation.

But something went wrong in your district and phone connection dropped. At all. And now you do not know if a game is eventually confirmed or canceled.

Your options in that case:
* use the latest information which you have and go to the soccer field at 4 pm as it was previously agreed
* wait for the newest data and stay at home; you do not want to come to the field if there's no match

You do not know what decide the other team. And you're not sure about your teammates. Eventually, the game did not happen because the teams did not have a plan how to act in such case. And each team did not have a needed quorum on the field: less than 5 players came from the each side. The opportunity to improve soccer skills is lost.


Choosing consistency
--------

You met the next day in school and discussed the situation that some players came to the match and some did not. Now you have a plan - if a game is preliminary agreed and phone does not work - you go to the soccer field anyway. Also, **Real Bits** team invited your team for a today's game at 3:30 pm.

When you come home you find that the phone still does not work. A player from **Real Bits** team comes to you at 2 pm, apologies and tells that they cannot make it today. You run to your friends and share the update that there is no game today. Since the phone line does not work, you spent 30 minutes to literally run to them and return home.

> Next: some other team come and schedule a new game for 5:30 pm


0. Use all goodness from other articles
1. Explain for soccer, training, all live on one street, 10 years old
2. What is C, A, P
3. It's not black and white - we can have a trade-off
4. practical usage: various databases
5. it was only intro
6. Add other articles as the reference

P is selected by default
Actually C or A.

Why services need Health Checks
-----

Something

Classic 'You call me'
-----

HTTP-based Heatlh Checks
Something

NoHTTPS or Not Only HTTP-servers
-----

Something

Modern 'I call you'
-----

Something

Hipster 'You kill me'
-----

Something

Summary
-----

Hey Hey


_P.S. Something._

**What's your own experience of scaling MongoDB?**

**How do you shard your databases?**
