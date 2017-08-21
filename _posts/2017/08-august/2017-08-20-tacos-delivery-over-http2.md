---
title: "🌮 Tacos Delivery Over HTTP/2"
layout: post
date: 2017-08-20
location: Austin, Texas
description: |
 Explaining pros of switching to HTTP/2 using Tacos Delivery as the example. Do not read if you're hungry.
comments: true
tags: [http, tacos]
og_image: /images/taco-delivery-over-http-latest.png
keywords:
  - http 2 by example
  - http 2 explained
  - python http 2 support
  - reverse proxies http 2 support
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/taco-delivery-over-http-latest.png"
      alt="Tacos Delivery Over HTTP/2"
      class="image-right"
      width="250"
      height="345"
      layout="fixed">
  </amp-img>
</div>

Recently I looked into [HTTP/2](https://en.wikipedia.org/wiki/HTTP/2){:target="_blank"} and its comparison with [HTTP/1.1](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol){:target="_blank"}. The adoption of the technology is growing - 16.1% of top Alexa websites already use the latest version of the protocol.

I wanna understand HTTP/2 better. For now, I do not see any project to apply the technology in production. But we all know that another great way to learn something - is to teach somebody else. It happened that y'all are selected as the audience for that :)

In teaching and learning, it's vital to keep things interesting.

Tacos are definitely not boring.
In the blog post, we will try to imagine that we live in the world where **web servers deliver tacos instead of HTML-pages**. Let's contemplate pros of serving this delicious [Tex-Mex](https://en.wikipedia.org/wiki/Tex-Mex){:target="_blank"} food over HTTP/2 instead of regular HTTP/1.1.

 *Do not read it if you're hungry!*



<!--more-->

----

One day in the life of a taco
-----

A process of taco delivery is not very straightforward. Let's revise it:
1. You realize that you're hungry.
2. You choose the one which is the best for you.
3. You ask a chef to cook it.
4. The chef cooks the food for you.
5. You wait and polls chef for the status of your order since you're hungry.  
6. It's ready! You provide delivery address and instructions, like gate code, etc.
7. Chef gives your order to a courier.
8. You wait and poll courier for the status of your order since you're very hungry...
9. Courier arrives and brings your tacos. But it's not the end.
10. You pay for the service to courier with cash. Now you can eat. And you eat.
11. Courier takes your money and brings that to Taco Shop.
12. You find out that you need more tacos because you're still hungry... Go to the step 2 :)

In this exaggerated example, you can easily see request/response pattern: your action leads to a reaction from another side. And you cannot initiate another action until the previous one isn't processed (well, it depends).

In the next three sections, we will review which techniques from HTTP/1.1, HTTP/2 and HTTPS can be used to improve your experience as a taco-eater. Each point below has the following structure: the ironic example from Tex-Mex cuisine and reference to real feature of a protocol.

HTTP/1.x as a baseline
-----

If your Taco Shop uses HTTP/1.0 you need to wait between ordering the next taco while a chef cooks a one for you. But **with HTTP/1.1 you can order bacon and eggs taco and just after that a migas one without waiting for the first one be cooked**. Note, they **must be** served in a predefined order. Even if for some reason it's more convenient for you to customize it. This feature of HTTP/1.1 is called [HTTP pipelining](https://en.wikipedia.org/wiki/HTTP_pipelining){:target="_blank"}.

**A number of tacos delivered by one Taco Shop to the same address is limited**. We have a state law ([RFC-2616](https://www.ietf.org/rfc/rfc2616.txt){:target="_blank"}) to limit this to 2, but the most modern Taco Shops do not respect it, so, the actual value is about 6. When people need moar tacos they work around this by splitting their requests between different taco vendors or locations. Taco Shops open affiliated branches to satisfy the rule like *pork.taco.shop*, *chicken.taco.shop*, and *vegan.taco.shop*. In that case, businesses need to maintain all these locations. This workaround from the HTTP/1.1 world is well-known as [Domain Sharding](https://blog.stackpath.com/glossary/domain-sharding/){:target="_blank"}.

**Tacos taste the best with sauces**. If your Taco Shop uses HTTP/1.x, they send you all sauces even if you do not need some of them. That's it: if a Taco Shop serves 12 types of tacos and each of them is served with a different one - they give you all of them. Even if your order consists only from 1 taco. This is done to decrease the number of sauce requests. The downside of that - a courier needs to carry more stuff than you need. It leads to some delays of tacos delivery. The example refers to [Image Sprites](https://www.w3schools.com/css/css_image_sprites.asp){:target="_blank"}.

**A courier can carry only one plate with food at a time**. So, if you have a party, all your orders are placed together for the fastest delivery. Yes, vegan taco, pork one, and chile con queso can be touching each other on the plate. It can be not very convenient :). I refer to [the concatenation of CSS and JS files](https://hacks.mozilla.org/2012/12/fantastic-front-end-performance-part-1-concatenate-compress-cache-a-node-js-holiday-season-part-4/){:target="_blank"} and [assets inlining into HTML pages](https://varvy.com/pagespeed/inline-small-css.html){:target="_blank"}.


Why HTTP/2 can feed you better
-----


**You need fewer words to explain your needs**. Taco Shop can get you with a half word. It definitely saves time in a long-run. Yay, [Header compression](https://http2.github.io/faq/#why-do-we-need-header-compression){:target="_blank"}!

**Your tasks can be prioritized**. You can manage the order of tacos which you get, it's not necessary first-in-first-out order. This is done thankfully to [Streams and Prioritization](https://chadaustin.me/2014/10/http2-request-priorities-a-summary/){:target="_blank"}.

**If all customers enjoy the taco which you order with some additional dip better - you get it without any ask**. If you do not want it today, or have some allergy - you can easily reject it. Convenient, right? Oppositely, in HTTP/1.x world you need to be explicit about all your needs. If you forgot to ask for salsa or guacamole, you just do not get it. Even if the chef knows that all customers prefer them served with the tacos. This an allusion on [Server Push](https://en.wikipedia.org/wiki/HTTP/2_Server_Push){:target="_blank"} from HTTP/2 world. Do not mix that with Server-Sent Events and WebSockets.

**The number of tacos delivered to one address is not limited by the state law ([RFC-2616](https://www.ietf.org/rfc/rfc2616.txt){:target="_blank"})**. Now a courier can bring as many tacos as you need. Therefore, Taco Shops do not need to have other locations to serve different types of tacos. Having [Multiplexed support](http://qnimate.com/what-is-multiplexing-in-http2/){:target="_blank"} in HTTP/2 we use a single TCP connection for all requests. This means that we do not have a need in [Domain Sharding](https://blog.stackpath.com/glossary/domain-sharding/){:target="_blank"} as it was with HTTP/1.1.

**Each taco is placed on a separate plate**. Putting all the food for one order together in one box does not make any sense now. No need of inlining.

**A courier brings the sauces only for the tacos which you ordered**. No need in CSS spriting. We lazily load only mandatory data.


A few words about HTTPS
-----

This topic worth a separate blog post. For now, I wanna limit it to the context of tacos. You can have secured HTTP connections with both versions of HTTP protocol: HTTP/1.1 and HTTP/2. It provides the following benefits for us as the taco-eaters.

**Nobody knows what you order except your Taco Shop**. If you tell all your friends that you're a vegan and wanna keep the image, but like to eat pork tacos secretly: you definitely need to keep your requests encrypted. Both for HTTP/1.1 and HTTP/2 it's optional, but with HTTP/2 it's mostly used. This is also known as Confidentiality.

**It's ensured that your deliveries include all the things which you ordered and only them**. No substitutions and nobody can eat or even bit your taco. Or add an ingredient which you do not like or allergic. You always know that it's cooked by the certified Chef which you trust. People in suits call this Authenticity.

**You pay for tacos and you want the money to be delivered to the Taco Shop which kindly served for you**. If your HTTP connection is secured, nobody can steal your bucks. Integrity makes this happen.

Summary
---------

Delivering tacos over HTTP/2 provides better service to customers thankfully the features like Multiplexing,  Streams, Prioritization, and Server Push. At the same moment, it simplifies business for Taco Shops: they can remove workarounds introduced for HTTP/1.x which slows down development speed and increases cost of maintenance.

Internet giants like Facetaco, Buritter, and YourTexMex already use the newest standard. But this fact does not mean you should blindly follow them. Changes like that require investment in your backend.  

I've done a quick look into technologies which can help your product if you decide to make the step forward HTTP/2. From the Python side I've got the following:
* [Twisted](http://twistedmatrix.com/pipermail/twisted-python/2016-July/030535.html){:target="_blank"} starting 16.3 supports that. Who uses Twisted in 2017? Asynchronous Python projects started a decade ago locked down by the technology. I've been using Twisted in 2014-2016.
* [Hyper-h2](https://python-hyper.org/h2/en/stable/){:target="_blank"} is an HTTP/2 protocol stack, written entirely in Python. The goal of the project is to be a common HTTP/2 stack for the Python ecosystem, usable in all programs regardless of concurrency model or environment. Never tried this, but looks interesting: they have examples for [asyncio](https://python-hyper.org/projects/h2/en/stable/asyncio-example.html){:target="_blank"}, [Twisted](https://python-hyper.org/projects/h2/en/stable/twisted-example.html){:target="_blank"}, [Eventlet](https://python-hyper.org/projects/h2/en/stable/eventlet-example.html){:target="_blank"}, [Curio](https://python-hyper.org/projects/h2/en/stable/curio-example.html){:target="_blank"}, [Tornado](https://python-hyper.org/projects/h2/en/stable/tornado-example.html){:target="_blank"}, and [WSGI](https://python-hyper.org/projects/h2/en/stable/wsgi-example.html){:target="_blank"}.
* [Django](https://github.com/django/daphne/issues/30){:target="_blank"} supports HTTP/2.
* aiohttp does not support HTTP/2 yet.

Anyway, it's recommended to put a reverse-proxy on the edge, in front your HTTP-server written with Python or another programming language. The reverse-proxy keeps HTTP/2 connections with clients and establishes HTTP/1.x connection with the web server which you built.

The most popular reverse-proxies:
* Open Source version of [NGINX](https://www.nginx.com/blog/nginx-1-9-5/){:target="_blank"} supports HTTP/2.
* AWS put support of HTTP/2 into [Application Load Balancer](https://www.nginx.com/blog/nginx-1-9-5/){:target="_blank"}.
* Apache HTTP server supports the protocol in [mod_http2](https://httpd.apache.org/docs/2.4/mod/mod_http2.html){:target="_blank"}.
* [Varnish](https://info.varnish-software.com/blog/how-its-made-varnish-5.0-and-http/2){:target="_blank"} has experimental support since 2016.
* [HAProxy](http://haproxy.formilux.narkive.com/DICXg7vW/announce-haproxy-1-7-dev6#post2){:target="_blank"} does not have official support yet.

I think that switching from HTTP/1.1 to HTTP/2 should be doable. Well, I'm done with the topic for today. Time to order some tacos.

-----

**What is your favorite taco?**

**Sorry, I meant, do you use HTTP/2 in production?**
