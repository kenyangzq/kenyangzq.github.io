---
title: Many Faces of Publish/Subscribe
date: 2018-02-05 16:01:28
tags: 
- Distributed System
categories:
- Study Note
- Distributed System
---

# Many Faces of Publish/Subscribe

Study note for the paper [*The Many Faces of Publish/Subscribe*](http://homepages.gsd.inesc-id.pt/~ler/docencia/tm0405/papers/PublishSubscribe-Eugster.pdf)



### 3 Dimensional Decoupling by Pub/Sub

Publish / Subscribe pattern enables a 3-dimensional decoupling:

1. Time:

   Pub and sub do not have to be up all the time. They can come and go like P2P. 

2. Space:

   They do not need to be aware of each other. Don't share their locations (references).

3. Synchronization:

   Nobody blocks the other.

The decoupling in these dimensions increases scalability, improves resilience and reduce coordination.



### Some Other Communication models

- Message passing
  - Low level approach to send/receive messages
- Remote Procedure Call (RPC)
  - Builds over message passing
  - Appears as though a normal procedure call is made but it is a remote invocation
  - JAVA RMI, CORBA request-response, SUN RPC, DCOM
- Tuple spaces (distributed shared memory)
  - Supports the anonymous, decoupled messaging across time and space BUT not the synchronization decoupling that pub/sub provides
- Message queuing
  - More of an implementation detail of the message passing protocol
- Notification
  - Provide synchronization decoupling but not on time and space. 



### Types of Pub/Sub Models

##### Topic-based

Publishing/subscription is based on events that are named or have an ID.

Every topic will have a unique name. The interfaces for both pub and sub are simple. Supports hierarchical addressing, which means topics can form hierarchy through containment. 

Note, the subscriber will receive everything from the topic it subscribes and the filtering of the event is the responsibility of the subscribers. 

##### Content-based

Publishing/subscription is based on the contents of the event, which provides finer granularity. But the implementation is more complicated and cause more overhead. 

Now the filtering is the responsibility of the whole mechanism but not subscribers.

##### Type-based

Like topic-based but provides a programming language-level type instead of some name. Events become type-safe, i.e. compile time error checking. 

Filtering is still subscriber's work as in topic based. 

##### Data-centric

Another model that uses typed topics and also deals with content. 

It's a hybrid of the previous 3 models. 



### Implementation Concerns

##### Event

Can have message-based and invocation-based.

##### Media

- The architecture:
  - Central
  - Distributed
- Dissemination:
  - Point to point
  - Multicast

Centralized approaches generally use point 2 point and provide strong guarantee rather than high throughput and scalability. 

Distributed systems often use reliable protocols to propagates events to all or a subset of the brokers.  



##### Quality of Service

- Persistance of the events
- Priorities between events
- Transactional semantics for group of events
- Reliability