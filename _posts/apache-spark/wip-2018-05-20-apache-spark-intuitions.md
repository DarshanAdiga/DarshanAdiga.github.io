# Apache Spark - Intuitions
This is *yet another [Apache Spark](https://spark.apache.org/) post* found on the internet. I hope to make it different than most of the existing blogs/posts/tutorials about Apache Spark.

This post will have intuitions and insights about Apache Spark and mostly emphasises the fact that *Apache Spark* is indeed a great tool when it comes to Big Data.

### Target audience
This is write-up is second part of my previous post on [Distributed processing Patterns](apache-spark/2018-05-01-distributed-processing-patterns.md), so please go through that post first if you haven't yet. Understanding of the design patterns discussed in that previous post is refered in this post almost everywhere.

Most of the concepts in this post will be helpful for developers who have no or intermediate level of understanding about Apache Spark. Optionaly, a basic understanding of any distributed storage system like [HDFS (Hadoop Distributed File System)](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) would make this post more helpful.



## Content
- Apache Spark key concepts
- Distributed processing patterns in Apache Spark
- Key points about scalability
- Common pitfalls


## Distributed processing patterns in Apache Spark

Let us first get familiarize ourselves with the jargons of Apache Spark. Also, if you remember the design patterns discussed in the [previous post](apache-spark/2018-05-01-distributed-processing-patterns.md), you should be able to related to those patterns here.

#### Resilient Distributed Datasets (RDD)

RDD is an [abstraction](apache-spark/2018-05-01-distributed-processing-patterns.md#Abstraction) of [immutable](apache-spark/2018-05-01-distributed-processing-patterns.md#Immutability) data that needs to be processed by Spark applications. This abstract nature of RDD makes it so versatile that it can actually represent any type of, any amount(literally TBs or PBs of data) of data and the data could be distributed across hundreds or thousands of machines! Such a powerful representation of data is achieved because **RDD is not the data, it is just an abstraction of data**.

Another abstraction that lies within an RDD is **partition**. An RDD is divided into *n* parts and each of them will be a partition. The **Divide and conquer** pattern discussed in the [previous post](apache-spark/2018-05-01-distributed-processing-patterns.md) can be related here.

#### Transformations and Actions

Any operation that you want to perform on an RDD(on the data represented by an RDD, to be specific) will be categorized as either a Transformation or an Action.

Transormations are nothing but functions that take can be applied on an RDD and produces another new RDD. You can relate transformations to **Immutability**, **Iterator** and **Pipelining** patterns of the [previous post](apache-spark/2018-05-01-distributed-processing-patterns.md).

Sequence of transformations create a sequence of RDDs which become a Directed Acyclic Graph(DAG). Each RDD in the graph becomes a node and has one or more RDDs as parent nodes. Transformations are represented as edges of the DAG. 

The documents flow from top most RDD(root node) till the bottom most RDD(leaf node) and under go all those transformations defined as edges. This is where **iterator** and **pipelining** patterns come into picture.

Important observation here is that, unless somebody tries to pull a document from a child RDD, the child RDD will not as the parent RDD for a document and thus, the edge connecting these two RDDs will not be executed. This means, **that transformation edge will be executed at all!** So, **if the DAG is not connected, the flow of documents will be disconnected** and the documents will not be processed at all! That is what **Lazy evaluation** is about as discussed in the [previous post](apache-spark/2018-05-01-distributed-processing-patterns.md).

TODO About actions

#### Bringing the Code to Data



## Key points about scalability



## Common pitfalls



### References

- https://techvidvan.com/tutorials/features-of-hdfs/