---
title: MapReduce
date: 2017-06-13 22:59:06
categories:
  - Study Note
  - Bigdata
  - Hadoop
tags:
  - Bigdata
---

# MapReduce

MapReduce is a programming model and associated implementation for processing and generating large data sets. Apparently it consists of two parts: map and reduce. 

- Uses map function to generate a set of intermediate key/value pairs
- Uses reduce function to merge all intermediate values associated with the same intermediate key

There are three key ideas behind MapReduce.

### Parallelism

Map and reduce are parallel in nature. Each map function can handle its own data and so as reduce. However, reduce must wait till all maps finish, so need a barrier between these two phases.

In addition, in the intermediate phase between map and reduce, usually shuffle the intermediate result to reduce them more efficiently.

### Abstraction

The MapReduce paradign turns everything into two simple function, map and reduce. So developer won't have to worry about the other details. 

<img src="/images/MapReduce/mapreduce2.gif">

MapReduce provides map and reduce abstract programming interface. Map interface takes key/value pair that stores data as input, process it and spit out key/value pairs as output.

```python
map: (k1,v1) -> [(k2, v2)]
```

Reduce takes all those pairs and combines the values for the same keys. Then it finally gives the output of same form.

```python
reduce: (k2, [v2]) -> [(k3, v3)]
```

### Uniform Architecture

In previous parallel computing framework, developers need to consider storage, partition, distribution, collection and fault tolerance. But with MapReduce, they are freed from those troubles because it gives a uniform architecture to do all the dirty works.



## How MapReduce Works

MapReduce consists of 4 main components. 

- Client: the one that submits the MapReduce program. Every job on client will be packed with configuration files into jar form and stored on HDFS by JobClient.
- JobTracker: it coordinates the tasks. It's actually a java program. It communcates with clients, accept command from clients, monitor the heartbeat of TaskTracker and schedule the jobs based on schedule algorithm.
- TaskTracker: it runs the tasks. It's also a java program which sends heartbeat to TaskTracker periodically.
- File System: generally will prefer HDFS. 

One can view JobTracker as the Master and TaskTracker as the Slave in traditional MS control paradigm.

