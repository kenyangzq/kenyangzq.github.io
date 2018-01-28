---
title: GFS-1
date: 2017-10-16 21:19:53
tags:
- Bigdata
categories:
- Study Note
- Bigdata
---



# Google File System Note 1

　　During my lesure time, I plan to review some of my big data paper. I will try to write study note for some of the paper that I find important or interesting. This is the note for the first half of the famous Google File System paper [1].



## Introduction

　　There are several traindtional choices that the authors decided to reexamined and they wanted to  explore radically different points in the design space. 

1. Component failures are the norm rather than the exception. 

There are all sorts of possible failures. Constant monitoring, error detection, fault tolerance and automatic recovery must be integral to the system.

2. Files are huge. 
3. Most files are mutated by appending new data rather than overwriting existing data.
4. Co-designing the applications and the file system API benefits the overall system by increasing flexibility. 





## Design Overview

#### Assumptions

They have listed quite a few assumptions. Of those, I extract a few that catch my attention

- There are typically two types of reads: 
  - Large streaming reads
  - Small random reads
    - Performance-conscious applications often batch and sort their small reads.
- The system must efficiently implement well-defined semantics for mutiple clients that concurrently append to the same files. (Spoiler: I believe they use *record appen*d to handle this problem)
- High sustained bandwidth is more important than low latency. 



#### Interface

　　GFS supports the usual **CRUD** operations, where they name them as *create, delete, open, write, close, and read*. Moreover, GFS has *snapshot* and *record* *append* operations. They are discussed later. 



#### Architecture

　　A GFS cluster consists of a single master and multiple chunkservers. The architecture is shown below. 

<img src="/images/Papers/GFS-architecture.png">		

　　Files are divided into fixed-size *chunks*, typically 64 MB. Each chunk is identified by an immutable and globally unique 64 bit chunk handle assigned by the master at the time of chunk creation. For reliability, each chunk is replicated on multiple chunkservers.

　　The master maintains all the metadata. It periodically communicates with each chunkserver in *HeartBeat* messages to give instructions and collect its state.

　　Clients interact with the master for metadata operations, but all data-bearing communication goes directly to the chunkservers.

　　Neither the client nor the chunkserver caches file data. Clients do cahce metadata, however. These metadata are used to locate chunkservers and are sent from the master.



#### Single Master

　　Since there is only one master, GFS must minimize its involvement in reads and writes so that it won't become a bottleneck. The client get information of chunkserver from master and directly interact with those servers. 

　　For example, to perform a simple read, the client first translates the file name and byte offset into a chunk index with the file according to the fixed size of chunks. It sends the master with this information and gets back the corresponding chunk handle and its replicas' locations. Then the client contacts directly with chunkserver for the subsequent operations, and master is not involved.



#### Chunk Size

　　The authors suggested 64 MB chunk size. GFS uses lazy space allocation to avoid wasting space due to internal fragmentation. 

There are several advantages with a big chunk size:

1. It reduces clients' need to interact with the master as reads and writes on same chunk only need one initail request. 
2. It reduces network overhead by keeping a persistent TCP connection. 
3. It reduces the size of metadata stored on master.

There is also a disadvantage:

- Small files on only one chunk may turn it into a hot spot when many clients are accessing it at the same time. 



#### Metadata

　　The master stores three major types of metadata in its memory: the file and chunk namespaces, the mapping from files to chunks, and the locations of each chunk’s replicas.	The first two types are also kept persistent by logging mutations to an operation log stored on the master’s local disk and replicated on remote machines.

##### In-Memory Data Structure

　　Master operations are fast as metadata are in memory. Furthermore, the master periodically 	scan through its entire state in the background.


　　One may concern that the system is limited by the memory size. In fact this is not a serious limit as one chunk only needs less than 64 byte to store. 	

##### Chunk Locations		

　　The master does not keep a persistent record of which chunkservers have a replica of a given chunk. It simply polls chunkservers for that information at startup.

##### Operation Log

　　This is the only persistent record of metadata on GFS. It also servers as a logical time line that defines the order of concurrent operations. It's replicated on multiple remote machines and respond to a client operation only after flushing the corresponding log record to disk both locally and remotely.

　　The master recovers its file system state by replaying the operation log. To keep the log small, the master checkpoins its state whenever the log grows beyond a threshold. The checkpoint is a compact B-tree like form that can be directly mapped into memory. 



#### Consistency Model

GFS has a relaxed consistency model. 

##### Guarantees by GFS

Two definitions:

- A file region is consistent if all clients will always see the same data, regardless of which replicas they read from.
  - A region is defined after a file data mutation if it is consistent and clients will see what the mutation writes in its entirety.	

After a sequence of successful mutations, the mutated file region is guaranteed to be defined and contain the data written by the last mutation.



## System Interactions			

The GFS system is designed to minimize the master's involvement in all operations.

#### Lease and Mutations Orders		

　　A mutation is an operation that changes the contents or metadata of a chunk such as a write or an append operation. GFS uses **lease** to maintain a consistent mutation order across all replicas. 			

　　The master grants a chunk lease to one of the replicas, which we call the *primary*. The primary picks a serial order for all mutations to the chunk. All replicas follow this order when applying mutations.

​			<img src="/images/Papers/GFS-dataflow.png" width="400" class="center">		

The figure above shows the detailed order of write control and data flow. 		



#### Data Flow	

　　To fully utilize each machine’s network bandwidth, the data is pushed linearly along a chain of chunkservers. Each machine forwards the data to the “closest” machine in the network topology that has not received it. Once a chunkserver receives some data, it starts forwarding immediately. 



#### Atomic Record Appends

　　In a record append operation, the client only specifies the data. GFS will append to the file *at least once atomically* at an offset GFS choosing and returns that offset to the client. 

When record append, need to check whether the chunk will exceed the maximum size. 

- If so, insert padding to the chunk and its replicas. Then informs the client that the operation should be retried on next chunk. (RA is restricted to be at most one-fourth of the chunk size.)
- If fits inside the chunk, the primary appends the data, informs the secondaries to do the same and finally replies success to the client. 

GFS does not guarantee that all replicas are bytewise identical. It only guarantees that the data is written at least once as an atomic unit.



#### Snapshot

　　The snapshot operation makes a copy of a file or a directory tree almost instantaneously. Uses **copy-on-write** techniques to implement. 

To perform snapshot:

1. Revok all the leases on the chunks in which files are about to snapshot.
2. Then master logs the operation to the log and duplicate the metadata for the source file or directory tree.
3. When client write to the snapshot, copy all the replicas locally by creating new chunks on the same chunkserver. 



## Reference

1. Ghemwat, Sanjay, Howard Gobioff, and Shun-Tak Leung (Google). *“The Google*
   *File System.”* Symposium on Operating Systems Principles (SOSP) ’03,
   Association of Computing Machinery. Published 2003.