---
title: "Scaling MongoDB by Kristina Chodorow"
layout: post
date: 2017-08-06
location: Austin, Texas
description: |
 Reading notes and book review of "Scaling MongoDB". The short book is about sharding and spinning a robust MongoDB cluster.
comments: true
tags: [reading notes, distributed, database, mongodb]
og_image: /images/scaling-mongodb-book-cover.jpg
keywords:
  - sharding mongodb
  - scaling mongodb
  - reading notes
  - mongodb shard key
  - book review
---

<!-- <img
src="{{ site.cdn.http }}/images/scaling-mongodb-book-cover.jpg"
width="200"
style="float: right; padding-left: 1em; padding-bottom: 1em; padding-top: 0.5em;"
alt="Scaling MongoDB. Book cover"/>
</a> -->
<!-- class="image-right" -->

<div class="image-wrapper">
<a href="http://shop.oreilly.com/product/0636920018308.do" target="_blank">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/scaling-mongodb-book-cover.jpg"
      alt="Scaling MongoDB. Book cover"
      class="image-right"
      width="250"
      height="345"
      layout="fixed">
  </amp-img>
  </a>
</div>


A single-server database can be only in 2 states: up or down. But [Mongo DB Is Web Scale](http://www.mongodb-is-web-scale.com/){:target="_blank"}, right? :)

When my wife found [the book](http://shop.oreilly.com/product/0636920018308.do){:target="_blank"} in our city library, she offered me to read it because I'm interested in learning and using various data stores. And the book is 50ish pages so why not read it in a couple of hours?

Being not an expert in [MongoDB](https://www.mongodb.com/){:target="_blank"} at the moment of reading (_and only reading does not make me an expert_) I found some interesting facts about the sharding of document-oriented solutions.

Check my summary and reading notes below. I rated the book 4 of 5.

<!--more-->

The book is easy to read; it has good examples, ideas in the book look practical, applicable and have common sense from my perspective. The most I liked the chapters _Understanding Sharding_ and _Setting Up a Cluster_. I did not use sharding of MongoDB in production yet, but after the book, I have a vision how I can do that. Probably, I expected to see more gotchas with scaling MongoDB covered.

**Disclaimer:** Before reading I used the storage only for a few small projects, and it worked fine.

What is _scaling_ in MongoDB world?
-----

Running a distributed data storage can be a source of the potential problems:
- Parts of a cluster cannot communicate with each other.
- A subset of machines crashes.
- Taking a consistent snapshot is not straightforward without a maintenance of the whole application.

[Shard](https://en.wikipedia.org/wiki/Shard_(database_architecture)){:target="_blank"} is one or more servers in a cluster that is responsible for some subset of data. If there is more than one server in a shard, each server has an identical copy of the subset of data.

MongoDB uses sharding as the method to split large collection across a cluster. Sharding is designed to reach the three goals:
- Make the cluster invisible from client's perspective. Interaction with a group of nodes looks the same as with a single-node server. The process responsible for the routing of queries is called `mongos`.
- Make the cluster always available for reads and writes.
- Let the grow cluster easily. Adding/removing nodes should be easy and automated.

As the consequences of the goals, a MongoDB cluster should be easy to use and administrate.

MongoDB cluster consists of the following components:

- `mongos` - a router, entry point for all queries.
- Shards with data
- Cluster config servers.

Each element is a process and does not require a dedicated machine.


How MongoDB performs sharding
------

MongoDB uses range-based splitting to distribute data across shards. Data is split into chunks of given ranges, like `['a', 'g')`. A range can be assigned only to one shard.

_MongoDB also [supports hashed and tag-aware sharding strategies](https://docs.mongodb.com/manual/sharding/#sharding-strategy){:target="_blank"} which were not available at the moment of writing the book. Check the documentation if you're interested in the types. In the blog post, we go only thru ranged sharding._

The method which MongoDB uses for partitioning the data between shards is a bit non-intuitive. Let's revise naive but not sufficient approaches for understanding pros of the recommended one.

**One range per shard**. Say, we have four shards and each of them is responsible for storing users which have usernames starting from the letters in ranges `['a', 'f')`, `['f', 'n')`, `['n', 't')`, `['t', 'z']`.  It's easy to understand; I remember that one time I designed something like that a while ago for a small system. But for large datasets, it works only with the uniform distribution of data between ranges. For other cases, it leads to a lot of data movements between shards to make the sharding working. The book has the great explanation of the consequences of using the approach. With pictures.

**Multi-range shards**. For example, we have the same four shards, but we assign two smaller chunks to each shard. Like, Shard 1 stores two chunks `['a', 'd')` and `['d', 'f')` instead of one larger range `['a', 'f')` as it was with the previous approach. The problems start when we add new shards - it requires a rebalancing of the whole cluster. We need to transfer some part of data from Shard 1 to Shard 2, say chunks for the`['e', 'f')` range, but to keep the Shard 2 balanced we're required to move some chunks from Shard 2 to Shard 3 and so on. Doing all the data transfer exercises in not a good thing for a large production system.

To distribute data between shards, MongoDB needs to know a **shard key**. Shard key is constructed from one or multiple fields of a document. In the very beginning, all data belongs to one chunk with the boundaries `(-∞, +∞)`. When we have inserted enough data for sharding, the data is split into two chunks. Every chunk must be distinct and belong only to one range without any overlapping. Each document in MongoDB must belong to only one chunk.

Note, that MongoDB does not allow to insert a document without a shard key because it does not know where to persist the document in the cluster. But it can have `null` value. Yes, we can have values of different types as a shard key. MongoDB has special rules for ordering objects of the various types in chunks:

 `null < numbers < strings < objects < arrays < binary data < ObjectIds < booleans < dates < regular expressions`

Shard keys are immutable; we cannot change a document in place. Instead of that, we need to get a document from the database, delete it, update the shard key and insert the updated record.

Choosing a sharding key for your cluster
-----

Picking a good sharding key is essential.

Using low-cardinality keys is not an excellent idea. Say, we decide to shard document by continents which are a set of low cardinality and contains `Africa`, `Asia`, `Australia`, `Europe`, `North America` and `South America`. We end up with six shards each of them will store chunks related to a continent. That is, we won't be able to shard data after some point which it's a dangerous consequence of "manual sharding".

If for some reason you need to specify a shard for a document manually - it's better to do not use sharding capabilities of MongoDB at all. You may think that using low-cardinality keys is a thing to provide data center awareness into MongoDB's sharding. But starting from 2011 the feature is [available out of the box](https://jira.mongodb.org/browse/SERVER-992){:target="_blank"}.
From another side, usage of low-cardinality keys can be reasonable if a collection has a lifetime, e.g. a new collection is created each week.

Another idea is to use ascending shard keys. For most applications, recent data is accessed more often than older data. You might see the opportunity to keep the recent data hot in RAM since reading there is faster than from disk. Using timestamp as a sharding key only looks like a smart idea - you will end up with having inserts only to the very last chunk of the latest shard. It's not recommended to use such keys for high load systems.

One more bad idea for busy systems - using random sharding key. Yes, in that case, our data are evenly distributed across the cluster. But we lose the ability to use index since our shard key means nothing from data ordering perspective. Not using index leads to extensive lookups when we try to find a record - fetches become slow and expensive.

Okay, you might ask the question (as I did):

> ### Is it possible to choose a good shard key at all?


Developers of MongoDB recommend using a [compound shard key](https://en.wikipedia.org/wiki/Compound_key){:target="_blank"} which is based on two fields. One of the fields must be coarsely ascending, and the other key should be something that the application commonly queries by. We can define the key as `{coarselyAscending : 1, search : 1}`. The book shows the example is using of `timestamp` as `coarselyAscending` key and `user` as a `search` key, like `{timestamp : 1, user : 1}`. Note, that using only ascending `search` key as a single key won't work and leads to enormous unsplittable chunks. If you're interested in selecting a good shard key for MongoDB, I highly recommend reading 23rd and 24th pages of the origin book. The last page lists questions which help to decide wisely.

One more thing, _a chunk of data_ is a logical concept, not a physical reality. The documents in a chunk are not physically contiguous on disk or grouped in any way. The default size of a chunk for a production system is 64MB. But for learning and development purposes you can specify any value for a chunk size.

Maintaining a MongoDB cluster
-----

Balancing is the migration of data from one shard to another one. It's a native capability of MongoDB. The goal of the balancing process - keep data evenly distributed and minimize the amount of data transfer. MongoDB does not start moving data after each created chunk. To start balancing a shard must have at least nine extra chunks. Note, that we can't make decisions about balancing rules.

Due to a distributed nature of cluster, we do not have a single _snapshot in time_. For example, we might have a counting problem: when we run a query to count documents in a cluster during migration, a document can be on two shards at the same time. One more caveat: for a unique index we might have not unique keys because we do not lock down the whole cluster during inserts. It would slow down the performance of the cluster.

To make a snapshot of the whole cluster, you should turn off the balancer to avoid duplicates. Typically, people take backups from individual shards. Also, do not use a single config server in production since it makes saving backups trickier.

`mongos` is a proxy process for accessing data from shards and redirects requests to a proper shard based on value of the shard key for the cluster. We should avoid querying shards directly. It's recommended to use the queries which require lookup thru only one shard to reduce the unnecessary load. `mongos` processes can be used for reads, writes, and administration of a cluster. `mongos` instances are stateless. Having additional `mongos` processes provides better reliability. Since they're stateless, it's not a big deal if some of them go down.

Adding and removing the capacity of a MongoDB cluster alters application's performance. It's better to add shards when you still have room to maneuver. A good thing - new shards do not need to be empty, you might have data from others collections there.

The idea which worth investigation: using a queue for controlling access to the MongoDB cluster. It helps when we perform maintenance and site is down; or when we have a bursty traffic. Also, you should design your application in a way which keeps it working even if a shard is unavailable.

_P.S. The book was published in early 2011, and my review might have some outdated information. Let me know in comments, and I will fix that._

What's your own experience of scaling MongoDB?
