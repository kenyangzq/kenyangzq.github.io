---
title: Hadoop Basic
date: 2017-05-21 18:27:16
categories:
  - Study Note
  - Bigdata
  - Hadoop
tags: 
  - Bigdata
  
---

# Hadoop Study Note 1

<br>

> The Apache Hadoop software library is a framework that allows for the distributed processing of large data sets across clusters of computers using simple programming models. 

Hadoop is built by Doug Cutting, the pioneer in the area of Bigdata. One fun fact, the name Hadoop is borrowed from Cutting's three-year-old son, and it is originally for his toy, a yellow elephant, which also becomes the well-known logo of Hadoop.

<img src="http://tse4.mm.bing.net/th?id=OIP.te5YCTFCrqh4N2S65P8cVgEpEs&pid=15.1" alt="Hadoop logo" width=100px, align=center></img>

Nowadays, Hadoop mainly refers to the enormous products in its ecosystem. 

## Main Components

### MapReduce
MapReduce is the core component of Hadoop.

> MapReduce is a programming model and an associ- ated implementation for processing and generating large data sets.

MapReduce consists of two main parts: Map function and Reduce function. It's first brought up by engineers from Google and inspired from functional programming languages. 

### HDFS
Hadoop Distributed File System (HDFS) is a open-source implementation of Google File System (GFS). It's specifically made for big data with high-throughput access to application data and is highly fault tolerant. In addition, HDFS can be deployed on relatively cheap machine. 

### Hive
> A data warehouse infrastructure that provides data summarization and ad hoc querying.

Hive provides complete functionality for SQL query. It transforms SQL into MapReduce task. Hive is specifically designed for statistical analysis. 


### HBase
HBase is a distributed, column-store database. It can be seen as a open-source implementation of Google's Bigtable. Unlike relational database, it supports storage for non-structured data. 

### Pig
Pig is a high-level programming language that, as its inventor puts, fits in the sweet spot between the declarative style of SQL, and the low-level, procedural style of MapReduce. 

As my Bigdata course lecturer says, when something doesn't work out very well, programmers just put an extra layer of abstraction on top of it. Pig is the one on top of MapReduce. It highly simplifies the code and free engineers from heavy work for simple analysis.

### ZooKeeper

ZooKeeper is the open-source implementation of Google's Chubby. It provides a high-performance coordination service for distributed applications. 


## Application of Hadoop

Hadoop is suitable for all of the following situations:

1. Huge volume of data. 
2. Offline data processing. It's well-known that Hadoop perform poorly in streaming data processing or real time analysis. 
3. Parallel computing. MapReduce works pretty well for parallel task, for example, web search for information.
4. Large files. Base on the design choice of HDFS, Hadoop is suitable for processing bigger files. 



--- 


## Reference
[1]. *MapReduce: Simplified Data Processing on Large Clusters*, Jeffrey Dean and Sanjay Ghemawat

[2]. http://hadoop.apache.org/  

[3]. *Pig Latin: A Not-So-Foreign Language for Data Processing*, Christopher Olston, Benjamin Reed and Utkarsh Srivastava