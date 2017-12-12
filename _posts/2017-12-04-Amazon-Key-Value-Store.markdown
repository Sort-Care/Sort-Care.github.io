---
layout: single
title: Dynamo
date: 2017-12-04 10:47 +0800
mathjax: true

excerpt: "Paper Review for Umass 590s"
header:
  teaser: /assets/images/economics.jpg
  overlay_image: /assets/images/economics.jpg
  overlay_filter: 0.5
---
{% include toc title="Contents" %}

# Demands
> Reliability at massive scale
> At a large scale, small and large components fail continuously and the way persistent state is managed in the face of these failures drives the reliability and scalability of software systems.

- Reliability: slightest outage has significant financial consequences and impacts customer trust.
- Scalability: support continuous growth.
- Amazon uses a highly decentralized, lossely coupled, service oriented architecture consisting of hundreds of services. There is paricular need for storage technologies that are **always available** (disks are failing, network routes are flapping, or data centers are being destroyed by tornados).
- Always be able to write to and read from
- Need to deal with failures in an infrastructure comprised of millions of components
- a select set of applications requires a storage that is flexible enough to let application designers configure.
- Writes are never rejected.

## Problems
- When to perform the process of resolving update conflicts

# Key Idea
**Dynamo**, a highly available key-value storage system that some of Amazon's core services use to provide an "always-on" experience.
- It is used to manage the state of services that have very high reliability requirements and need tight control over the tradeoffs between availability, consistency, cost-effectiveness and performance.
- the common pattern of using a relational database would lead to inefficiencies and limit scale and availability.
- Dynamo provides a simple primary-key only interface

## techniques used
- Data is partitioned and replicated using consistent hashing
- consistency is facilitated by object versioning
- consistency among replicas during updates is maintained by a quorum-like technique and a decentralized replica synchronization protocol.
- It uses a gossip based distributed failure detection and membership protocol.
- It is a completely decentralized system with minimal need for manual administration.
- Storage nodes can be added and removed from Dynamo without requiring any manual partioning or redistribution.

## Performance
It was able to scale to extreme peek loads efficiently without any downtime.

## Novel
- sacrifices consistency under certain failure scenarios.
- the reliability and scalability of a system is dependent on how its application state is managed.

Traditional replicated relational database systems focus on the problem of guarantee strong consistency to replicate data. Previous systems are not capable of handling network partitions because they typically provide strong consistency guarantees

### Differences
1. Always writeable
2. Built for an infrastructure within a single administrative domain where all nodes are assumed to be trusted.
3. applications that use Dynamo do not require support for hierarchical namespaces or complex relational schema
4. Dynamo is built for latency sensitive applications

## Contribution
- Evaluation of how different techniques can be combined to provide a single highly-available system.
- It demonstrates that an eventually-consistent storage system can be used in production with demanding applications.
- It also provides insight into the tuning of these techniques to meet the requirements of production systems with very strict performance demands.

# Background
**A relational database is a solution that is far from ideal**.

## System Assumptions and Requirements
1. *Query Model*: simple read and write operations to a data item that is uniquely identified by a key. State is stored as binary objects identified by unique keys.
2. *ACID Properties*: (Atomicity, Consistency, Isolation, Durability) is a set of properties that guarantee that database transactions are processed reliably.
3. *Efficiency*: The system needs to function on a commodity hardware infrastructure.

## Service Level Agreements(SLA)
Clients and services engage in a Service Level Agreement(SLA), a formally negotiated contract where a client and a service agree on several system-related characteristics.

**Amazon's engineering and optimization efforts are not focused on averages.**

## Design Considerations
- Dynamo targets the design space of an "always writeable" data store.(when)
- Who performs the process of conflict resolution.
  * Data store?: choices are rathre limited.
  * Application?: can decide on the conflict resolution method that is best suited for its client’s experience
  
### Other key principles embraced in the design
- *Incremental scalability*
- *Symmetry*
- *Decentralization*
- *Heterogeneity*

# System Architecture
**Core Techniques**
1. partitioning
2. replication
3. versioning
4. membership
5. failure handling
6. scaling

## System Interface
1. ```get(key)```
locates the object replicas associated with the *key* in the storage system and returns a single object or a list of objects with confliction versions along with a *context*

2. ```put(key, context, object)```
determines where the replicas of the object should be placed based on the associated *key*, and writes the replicas to disk. The *context* encodes system metadata about the object that is opaque to the caller and includes information.

## Partioning Algorithm
Dynamo's partitioning scheme relies on consistent hasing. Each data identified by a key is assigned to a node by hasing its key to yield its position on the ring, then walking clockwise to find the first node with a position larger than the item's position.

Each node becomes responsible for the region in the ring between it and its predecessor node on the ring.

**Advantage** of consistent hashing: departure or arrival of a node only affects its immediate neighbors and other nodes remain unaffected.

**Two challenges**
1. The random position assignment of each node on the ring leads to non-uniform data and load distribution
2. The basic algorithm is oblivious to the heterogeneity in the performance of nodes.

**Solution to these challenges**:
1. each node gets assigned to multiple points in the ring.
2. uses the concept of "virtual nodes"

## Replication
Each key, k, is assigned to a coordinator node.The coordinator is in charge of the replication of the data items that fall within its range. In addition to locally storing each key within its range, the coordinator replicates these keys at the N-1 clockwise successor nodes in the ring.

## Data Versioning
Dynamo provides eventual consistency, which allows for updates to be propagated to all replicas asynchronously.

> When a customer wants to add an item to (or remove from) a shopping cart and the latest version is not available, the item is added to (or removed from) the older version and the divergent versions are reconciled later.

Dynamo treats the result of each modification as a new and immutable version of the data.

It is allowed for multiple versions of an object to be present in the system at the same time. Dynamo uses vector clocks in order to capture causality between different versions of the same object.A vector clock is effectively a list of (node, counter) pairs. One vector clock is associated with every version of every object. One can determine whether two versions of an object are on parallel branches or have a causal ordering, by examine their vector clocks

## Exceution of ```get()``` and ```put()```
Selecting a node:
1. route is request through a generic load balancer that will select a node based on load information
2. use a partition-aware client library that routes requests directly to the appropriate coordinator nodes.
