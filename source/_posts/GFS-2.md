---
title: GFS-2
date: 2017-10-19 20:13:34
tags:
- Bigdata
categories:
- Study Note
- Bigdata
---

# Google File System Note 2

Continue from note 1. This note is also based on the Google File System paper [1].



## Master Operations

The master execute all the namespace operations. It makes placement decisions, creates new chunks and hence replicas, and coordinates various system-wide activities to keep chunks fully replicated, to balance load across all the chunkservers, and to reclaim unused storage. 



#### Namespace Management and Locking

To allow multiple master operation running concurrently, the master uses locks over regions of the namespace to ensure proper serialization.

GFS logically represents its namespace as a lookup table mapping full pathnames to metadata. Each node in the namespace tree, no matter a file name or a directory name, has an associated read-write lock.

Each master operation acquires a set of locks before it runs. It will acquire read-locks on all the related directory names, as /d1, /d1/d2, ..., /d1/d2/…/dn, and either a read or write lock on the full path name /d1/d2/.../dn/leaf. Note that the leaf can be a directory or a file. 

The read / write locks in GFS are the same as locks in other file system. Two things to note:

1. File creation does not require a write lock on the parent directory because there is no “directory” to be protected. A read lock is enough.
2. Locks are acquired in a consistent total order to prevent deadlock

One nice property of this locking scheme is that it allows concurrent mutations in the same directory.



#### Replica Placement

The chunk replica placement policy serves two purposes: maximize data reliability and availability, and maximize net- work bandwidth utilization.

GFS spreads chunk replicas across not only  machines but also racks to prevent from rack damage or offline. This also enables read for a chunk to exploit the aggregate bandwidth of multiple racks



#### Garbage Collection

When a file is deleted, GFS reclaim the physical storage azily during regular garbage collection at both the file and chunk levels.

##### Mechanism 

When a file is deleted, the operation is logged just liked others. However, the file is just renamed to a hidden name that includes the deletion timestamp. The file is actually removed during the master’s regular scan of the file system namespace after it exists for more than three days (configurable). When the hidden file is removed from the namespace, its in- memory metadata is erased.

This mechanism provide efficient way to undelete file.



#### Stale Replica Detection

For each chunk, the master maintains a chunk version number to distinguish between up-to-date and stale replicas. Whenever the master grants a new lease on a chunk, it increases the chunk version number and informs the up-to-date replicas.

Whenever chunk version number conflicts, always assume that the largest is up-to-date, even when the master's record is not the largest. 



## Fault Tolerace and Diagnosis

One of the greatest challenges in designing the system is dealing with frequent component failures.

#### High Availability

Two simple yet effective strategies: fast recovery and replication.

##### Fast Recovery

GFS does not distinguish between normal and abnormal termination. Both the master and the chunkserver are designed to restore their state and start in seconds.

##### Replication

For chunks, each chunk is replicated on multiple chunkservers on different racks. 

For master, its operation log and checkpoints are replicated on multiple machines. A mutation to the state is considered committed only after its log record has been flushed to disk locally and on all master replicas.

##### Shadow Master

GFS also support "*shadow masters*". They provide read-only access to the file system when the primary master is down. These shadows may lag the primary slightly, but they enhance read availability for files that are not being actively mutated or applications that do not mind getting slightly stale results.

To keep informed, a shadow master reads a replica of the growing operation log and applies the same sequence of changes to its data structures exactly as the primary does. It depends on the primary master only for replica location updates resulting from the primary’s decisions to create and delete replicas.



#### Data Integrity

Each chunkserver uses checksumming to detect corruption of stored data. As discussed in the first note, the semantics of GFS mutations, in particular atomic record append, does not guar- antee identical replicas. 

The paper does not discuss the **checksum** GFS uses. One typical and simple checksum algorithm is parity byte or parity word,  which breaks the data into "words" with a fixed number *n* of bits, and then computes the exclusive or (XOR) of all those words. The result is appended to the message as an extra word. To check the integrity of a message, the receiver computes the exclusive or of all its words, including the checksum; if the result is not a word with *n* zeros, the receiver knows a transmission error occurred. Detailed discussion of checksum can be found on [Wikipedia](https://en.wikipedia.org/wiki/Checksum).



## Measurements & Related Works

I will skip these parts as that's pretty outdated (2003) and they are also not the thing I want to learn from this paper. 





## Conclusion

In GFS, they treat component failures as the norm rather than the exception, optimize for huge files that are mostly appended to (perhaps concurrently) and then read (usually sequentially), and both extend and relax the standard file system interface to improve the overall system.

The system provides fault tolerance by constant moni- toring, replicating crucial data, and fast and automatic recovery. Additionally, we use checksumming to detect data corruption at the disk or IDE subsystem level.

GFS achieves high aggregate throughput by separating file system control, which passes through the master, from data transfer, which passes directly between chunkservers and clients. Master involve- ment in common operations is minimized by a large chunk size and by chunk leases.



## Reference

1. Ghemwat, Sanjay, Howard Gobioff, and Shun-Tak Leung (Google). *“The Google*
   *File System.”* Symposium on Operating Systems Principles (SOSP) ’03,
   Association of Computing Machinery. Published 2003.