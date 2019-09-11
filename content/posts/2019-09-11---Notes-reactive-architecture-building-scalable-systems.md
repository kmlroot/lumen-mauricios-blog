---
title: Introduction to Hyperledger Fabric
date: "2019-09-11"
template: "post"
draft: false
slug: "/posts/notes-reactive-architecture-building-scalable-systems/"
category: "reactive"
tags:
  - "Reactive Systems"
description: "Notes: Reactive Architecture: Building Scalable Systems."
---

## Consistency, availability, and scalability

A system is considered scalable if it can meet increases in demand while remaining responsive. A system is considered available if it remains responsive despite any failures. A system is considered consistent if it can response with the same data in any time, if all members of the system agree on the state, then the system is consistent.

Performance and scalability are related, but different concepts, performance optimizes response time, scalability optimizes ability to handle load. Performance means the time it takes to complete a single request (latency), scalability means the number of requests we can handle at a time (load). Request per second is a way to measure performance and scalability. Reactive microservices tend to focus on improving scaling because scalability has no theoretical limit.

### Consistency in Distributed Systems

The Problem with Distributed Systems:

- Distributed systems are systems that are separated by space
- Physics pusta an upper limit on information speed
- When two systems are separed by space, there is always time required to reach consensus
- In the time it takes to transfer the information, the state of the original sender may have changed

Everything's Eventual

- This means that the receiver of that information is always dealing with stale data
- This applies wheter we are talking about Computer systems, people, or even just the physical world
- Reality is therefore Eventually consistent

Eventual consistency guarantees that, in the absence of new updates, all accesses to a specific piece of data will eventually return the most recent value

Strong Consistency: An update to a piece of data needs agreement from all nodes before it becomes visible

**Amdahl's Law:** Show us The effect of contention on a distributed system. A key part of Amdahl's Law is to recognize that: Contention limits parallelization.

- Defines the maximum improvement gained by parallel processing
- Improves from parallelization are limited to the code that can be parallelized
- Contention limits parallalization and therefore reduce the improvements

In a system that is experiencing contention, **is false** that doubling the concurrency will double the throughput.

**Cohenrency Delay:** In a distributed system, synchronizing the state of multiple nodes is done using crosstalk or gossip, each node in the system will send messages to each node informing hem of any state changes, the time it takes for this synchronization is called the *Cohenrency Delay,* increasing the number of nodes increases the Cohenrency Delay.

**Gunther's Universal Scalability Law:** Increasing concurrency can cause negative returns due to contention and coherency delay.

- In addition to contention (Amdah's Law), it accounts for **Cohenrency Delay**
- Coherency Delay results in negative returns
- As the system scales up, the costs to coordinate between nodes exceeds any benefits

**Laws of Scalability:**

- Both laws (Amdahl's Law and Gunther's Law) demonstrate linear scalability is almost lways unachivable
- Linear scalability requires total isolation. Basically stateless
- Reactive systems understand limitations imposed by these laws
- They try to exploit the limitations, rather than avoiding them

### Scalability in Reactive Microservices

Reactive Microservices reduce **contention** by:

- Isolating locks
- Eliminating transactions
- Avoiding blocking operations

They mitigate herency delays by:

- Embracing eventual consistency
- Builiding in autonomy

**Stateless sytem:** No state is stored, either in the application, or the database.

### CAP THEROREM

- **Parition Tolerance:** The system continues to operate despite an arbitrary number of messages beign dropped by the network
    - No distributed system is safe from partitions
    - They can occur due to problems in the network
    - They occur when a node goes down, either due to failure or routine maintenance
    - They can be short, or long lived
    - They CAP Theorem is really about what happens when a partition ocurrs

    When a partition occurs, a distributed system has two options:

    - **AP** - Sacrifice Consistency: Allowing writes to both sides of the partition. When the partition is resolved you will need a way to merge he data in order to restore consitency.
    - **CP -** Sacrifice Availability: Disabling or terminating, one side of the partition. During the partition, some or all of your system will be unavailable

    The implications of the CAP theorem are that you have to pick between which of the following combinations: Consistent and Partition Tolerant and Available and Partition Tolerant

### Sharding for Consistency

Sharding partitions entities or actors in the domain according to their unique Id. Sharding partitions entities or actors in the domain according to their unique Id. - correct

- Sharding partitions entities (or Actors) in the domain according to their id
- Groups of entities are called a shard
- Each entity exists in only one shard
- Each entity exists in only one location
- Because entity lives in only one location, we eliminate the distributed system problem
- The entity acts as a consistenty boundary

As a rule of thumb, how many shards should we have in our cluster? Ten shards per node in the cluster.

#### Effects of Sharding

- Sharding doesn't eliminates contention, it isolates it
- Scalability is achieved by distributing the shards over more machines
- Strong consistency is achieved by isolating operations to a specific entity
- Careful choice of shard keys is important to mantain good scalability
- Sharding is primarily Consitent (CP) solution, therefore it sacrifies availability, it means, if a shard goes down is a period of time where it's unavailable

We can find contention in a sharded system in the coordinator and within a single entity. Every message bound for a sharded entity doesn't must first pass through the Shard Coordinator.
Users who are putting excessive load on the system will be more likely to experience contention. In a Sharded system, scalability is achieved by: Distributing the shards over a larger number of machines. In a Sharded system, Strong Consistency is achieved by: Isolating operations to a specific entity, which processes one message at a time.

### CRDTs (Conflict Free Replicated Data Types)

Provide a highly available solution based on asynchronous replication, CRDTs are highly available and eventually consistent, data is stored in multiple replicas for availability, updates are aplied to one replica and then copied asynchronously, updates are merged to determinate the final state.

With CRDTs data is copied asynchronously across multiple equal replicas.

The merge operation for CRDTs must be:

- Commutative
- Associative
- Idempotent

**Effects of CRDTs**

- CRDTs are store in memory, requires that the entire structure fit into avaiable memory
- Best used for small data sets, with infrequent updates, that require high availability
- They don't work with every single data type
- Some data types require complex merge functions

Choosing between consistency or availability isn't a technical decision, **it's a business decision.**

