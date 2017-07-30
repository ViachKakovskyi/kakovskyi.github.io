---
title: "Scaling MongoDB by Kristina Chodorow"
layout: post
date: 2017-07-30
location: Austin, Texas
description: |
 Reading notes and book review of "Scaling MongoDB". The short book is about sharding and spinning a robust MongoDB cluster.
comments: true
tags: [reading notes, distributed systems, mongodb]
---

TODO: add link to book cover and link to buy

From the beginning of the 2017 year, I was thinking about starting a blog. I had a few options for the main subject:
- BBQ
- Burrito
- Backend

Since I love all these things it was not the easy decision for me and it took half of the year to make it.
<!--more-->


Introduction
-----

1. Single-server database can be only in 2 states: up and down.
2. Potential problems with multiple servers:
- parts of a cluster cannot communicate with each other
- a subset of machines crashes
- taking a consistent snapshot is not straightforward without a maintenance of the whole application
3. Sharding is the method MongoDB uses to split large collection across a cluster. MongoDB does sharding almost automatically.
4. Sharding is designed to reach the 3 goals:
- Make the cluster invisible from client's perspective. Interaction with a cluster looks the same as with a single-node server. `mongos` process is responsible for routing.
- Make the cluster always available for reads and writes.
- Let the grow cluster easily. Adding/removing nodes should be easy and automated.
5. Consequences of the goals: a cluster should be easy to use and administrate.
6. **Shard** is one or more servers in a cluster that are responsible for some subset of data. If there is more than one server in a shard, each server has identical copy of the subset of data.
7. MongoDB uses range-based splitting to distribute data across shards. Data is split into chunks of given ranges, like `['a', 'g')`. **TODO: add link to interval notation**. A range can be assigned only to one shard.

Sharding of MongoDB
------

8. MongoDB uses a non-intuitive method of partitioning the data between shards. Let's revise naive but not sufficient approaches to understand pros of the recommended one:
- **One range per shard**. Say, we have 4 shards and each of them is responsible for storing users which have usernames starting from the letters in range `['a', 'f')`, `['f', 'n')`, `['n', 't')`, `['t', 'z']`.  It's easy to understand, I remember that one time I designed something like that a while ago for a small system. But for large datasets it works only with uniform distribution of data between ranges. For other cases it leads to a lot of data movements between shards to make the sharding working. The book has great explanation of the consequences of using the approach. With pictures.
- **Multi-range shards**. For example, we have the same 4 shards, but we assign two smaller chunks to each shard. Like, Shard 1 stores two chunks `['a', 'd')` and `['d', 'f')` instead of one larger range `['a', 'f')` as it was with the previous approach. The problems start when we add new shards - it requires rebalancing of the whole cluster: we need to transfer some part of data from Shard 1 to Shard 2, say `['e', 'f')`, but to keep the Shard 2 balanced we're required to move some chunks from Shard 2 to Shard 3 and so on. Not a good thing for a large production system.
9. To distribute data between shards MongoDB needs to knows a **shard key**. Shard key is constructed from one or multiple fields of a document. In the very beginning, all data belongs to one chunk `(-∞, +∞)`. When we have inserted enough data for sharding, the data is split into 2 chunks. Every chunk must be distinct and belong only to one range without any overlapping. Each document in MongoDB must belong to only one chunk.
10. MongoDB does not allow to insert a document without a shard key. But it can have `null` value. We can have values of different types for a shard key. MongoDB has a special rules for ordering objects of different types in chunks: `null < numbers < strings < objects < arrays < binary data < ObjectIds < booleans < dates < regular expressions`.
11. Shard keys are immutable, we cannot change a document in place. Instead of that we need to get a document from the database, delete it, update the shard key and insert the updated record.
12. A chunk is a logical concept, not a physical reality. The documents in a chunk are not physically contiguous on disk or grouped in any way. Default size of a chunk for production system is 64MB. But for learning and development purposes you can specify any value of a chunk size.
13. **Balancing** is migration of data from one shard to another one. It's a native capability of MongoDB. Goal of the balancing process - keep data evenly distributed and minimize amount of data transfer. MongoDB does not start moving data after each created chunk. To start balancing a shard must have at least 9 extra chunks. Note, that we can't make decisions about balancing rules.
14. `mongos` in a proxy process for accessing data from shards and redirects requests to a proper shard based on value of the shard key for the cluster. We should avoid querying shards directly. It's recommended to use the queries which requires lookup thru only 1 shard to reduce unnecessary load.
15. Architecture of a cluster:
- shards with data
- `mongos` - a router process
- cluster config servers.

Each component is a process and does not require a dedicated machine.

Setting Up a cluster
-----

16. It's essential to choose a good sharding key. Using low-cardinality keys is a bad idea. Say, we decide to shard document by continents which is a set of low cardinality and contains `Africa`, `Asia`, `Australia`, `Europe`, `North America`, `South America`. We end up with 6 shards each of them will store chunks related to a continent. That is, we won't be able to shard data after some point which it's a bad consequence of "manual sharding". If for some reason you need to manually specify a shard for a document - it's better to do not use sharding capabilities of MongoDB at all. You may think that low-cardinality keys is a thing to provide datacenter awareness into MongoDB's sharding. But starting from 2011 the features is available out of the box https://jira.mongodb.org/browse/SERVER-992.
From other side, usage of low-cardinality keys can be reasonable if a collection has a lifetime, e.g. a new collection is created each week.
17. Another idea is to use ascending shard keys. For most applications, recent data is accessed more often than older data. You might see the opportunity to keep the recent data hot in RAM since reading there is faster than from disk. Using timestamp as a sharding key only looks like a good idea - you will end up with having inserts only to the very last chunk of the latest shard. It's not recommended to use such keys for high load systems.
18. One more bad idea for busy systems - using random sharding key. Yes, in that case our data are evenly distributed across the cluster. But we lost the ability to use index since our shard key means nothing from data ordering perspective. Not using index leads to extensive lookups when we try to find a record - fetches become slow and expensive.
19. Is it possible to pick a good shard key at all? Developers of MongoDB recommend to use a compound shard key which is based on 2 fields. One of the fields must be coarselyAscending and the other key should be something that the application commonly queries by. We can define the key as `{coarselyAscending : 1, search : 1}`. The book shows the example is using of `timestamp` as `coarselyAscending` field and `user` as a `search` key, like `{timestamp : 1, user : 1}`. Note, that using only ascending `search` key as a single key won't work and leads to enormous unsplittable chunks. If you're interested in selecting a good shard key for MongoDB I highly recommend to read 23rd and 24th pages of the origin book. The pages list questions which help to make the decision wisely.
20. MongoDB provides ability to setup sharding both for a new collection and existing one.
21. Adding and removing capacity of a MongoDB cluster alters application's performance. It's better to add shards when you still have a room to maneuvr. A good thing - new shards do not need to be empty, you might have data from others collections there.
22. Due to a distributed nature of cluster we do not have a single "snapshot in time". For example, we might have a counting problem: when we run a query to count documents in a cluster during migration, a document can be on 2 shards at the same time. One more caveat: for a unique index we might have not unique keys because we do not lock down the whole cluster during inserts. It would slow down performance of the cluster.
23. Single update statement must have a shard key in a query criteria. In other case the database raises an error. But multi-update statement can be executed against cluster without errors.
24. MongoDB can be used in MapReduce mode.
25. `mongos` processes can be used for reads, writes and administration of a cluster. `mongos` instances are stateless. Having extra `mongos` processes provides better reliability. Since they're stateless it's not a big deal if some of them goes down.
26. To make a snapshot of the whole cluster you should turn off the balancer to avoid duplicates. Normally, people take backups from individual shards.
27. Do not use a single config server in production since it makes saving backups trickier.
28. A good idea is to use a queue for controlling access to the a MongoDB cluster. It helps when we perform maintenance and site is down; or when we have a bursty traffic. Also, you should design your application in a way which keeps it working even if a shard is unavailable.
