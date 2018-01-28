---
title: MapReduce-2
date: 2017-10-29 12:38:34
tags:
  - Bigdata
categories:
  - Study Note
  - Bigdata
  - Hadoop
---

# Second Look at MapReduce

Early I have written a very simple post about the idea of MapReduce. It was in fact part of the my lecture note in the Big Data course. Recently I re-read the paper, *MapReduce: simplified data processing on large clusters* [1], and I want to add some note to this topic. I will not follow the order of the paper and only write down some of the things that stands out, including my own thought and other related topics. 



## Programming Model

The interface is pretty simple: typically MapReduce is a imported library, e.g. [mrjob](https://github.com/Yelp/mrjob); user only needs to define the map and reduce function and pass them to the library to run. 

#### Execution Overview

As discussed in paper, input data is first partitioned into *M* splits for Map node; later on there will be *R* worker node for Reduce operation. Typically, number *M* and *R* are much larger than the number of machines. 

Each worker executes the map function user defined. And results are written to local disk periodically.  The master is responsible for a schedule plan to reduce transmission need. Typically, it will try to put reduce node near map node. In this way, most input data is read locally and consumes no nework bandwidth. 



**Note**:

1. One reason that Spark is much faster is that intermediate results are written to local disk, and Reduce nodes have to read from disk while Spark stores all the RDDs in memory.
2. Reduce nodes cannot start until all Map nodes finish. There is a Barrier between these two nodes. 



#### A Confusion

The paper indicates that after all the intermediate keys are received, they are sorted on the reduce node so that all occurance of the same key are grouped together. I don't quite unerstand how they achieve this. To me, it will make more sense for master to handle this. After all the intermediate results are read, master sorts them, possibly use external sort as data volume is too large to fit in memory. Then the sorted result is fed to Reduce nodes. But in this way the network bandwidth will be a restriction. 

*After discussion with a professor, I realized that the shuffle part between map and reduce is not covered in this paper. In fact, after mapper writes the data to local disk, it sorted the data. And the master shuffle the sorted results from each worker and then schedule the whole reduce task. This explains why there has to be a barrier between map and reduce.* 



## Fault Tolerance

#### Worker

The MapReduce library kind of supports a strong fault tolerance on worker node. But compared to Spark, it's too inefficient. 

On map task, if one fails, this task has to be re-executed in its entirety as the intermediate results are stored on local disks. But for reduce task, since the result is outputed to some global file system, (GFS with no doubt at that time, 2004), there is no need to re-start the whole reduce task.



#### Master

In the paper, they mention that they can use checkpoint as in GFS to maintain the master status. However, due to the small chance of failure, they decided to just abort the task. 

*I think they can also adopt the idea of shadow master from GFS. The primal master can record append the status and location of worker tasks to a chunk in GFS. When the master failed, the shadow master can continue from the chunk and handle the rest.* 

Just some naive thoughts.



## Others

The paper also mentioned how they take advantages of location information to better schedule the task, the task granularity. In addition, they handle straggler by scheduling multiple backup tasks. 

#### Combiner

In some cases when repetition in keys are frequent and reduce operation is *communative* and *associative*, MapReduce allows user to add a *Combiner* function to optimize. 

In my view, it's the same as a pre-reduce on Map operation. 



## Conclusion

In short, MapReduce is a very powerful programming model which pave the way for modern big data processing techniques. 



## Reference 

[1] Dean J, Ghemawat S. 2008. MapReduce: Simplified data processing on large clusters. Commun ACM 51: 107–113.

