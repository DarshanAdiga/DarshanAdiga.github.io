# Apache Spark - Intuitions
This is *yet another [Apache Spark](https://spark.apache.org/) post* found on the internet. I hope to make it different than most of the existing blogs/posts/tutorials about Apache Spark.

This post will have intuitions and insights about Apache Spark and mostly emphasises the fact that *Apache Spark* is indeed a great tool when it comes to Big Data.

### Target audience
This write-up is second part of my previous post on [Distributed processing Patterns](/distributed-processing-patterns/), so please go through that post first if you haven't yet. Understanding of the design patterns discussed in that previous post is refered in this post almost everywhere.

Most of the concepts in this post will be helpful for developers who have no or intermediate level of understanding about Apache Spark. Optionaly, a basic understanding of any distributed storage system like [HDFS (Hadoop Distributed File System)](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) would make this post more helpful.

## Content
- Apache Spark key concepts
- Distributed processing patterns in Apache Spark
- Key points about scalability
- Common pitfalls


## Distributed processing patterns in Apache Spark

Let us first get familiarize ourselves with the jargons of Apache Spark. Also, if you remember the design patterns discussed in the [previous post](/distributed-processing-patterns/#distributed-processing-design-patterns), you should be able to related to those patterns here.

#### Resilient Distributed Datasets (RDD)

RDD is an [abstraction](/distributed-processing-patterns/#abstraction) of [immutable](/distributed-processing-patterns/#immutability) data that needs to be processed by Spark applications. This abstract nature of RDD makes it so versatile that it can actually represent any type of, any amount(literally TBs or PBs of data) of data and the data could be distributed across hundreds or thousands of machines! Such a powerful representation of data is achieved because **RDD is not the data, it is just an abstraction of data**.

Another abstraction that lies within an RDD is **partition**. An RDD is divided into *n* parts and each of them will be a partition. The [**Divide and conquer**](/distributed-processing-patterns/#divide-and-conquer) pattern discussed in the previous post can be related here. Each partition is an indipendent collection of documents and collection of such partitions becomes an RDD.

Partitions know their ancestors! Yes, each partition in the RDD keeps track of the parent partition from which it was originated from. This is one of the design patterns in Apache Spark that blew my mind! [This page has an amazing explanation](https://github.com/rohgar/scala-spark-4/wiki/Wide-vs-Narrow-Dependencies) about narrow and wide dependencies of RDD. Do read it!

Any data processing operation applied on an RDD gets parallelized across all the partitions. So you get the point, **higher the number of partitions, faster is the processing**!

#### Transformations and Actions

Any operation that you want to perform on an RDD(on the data represented by an RDD, to be specific) will be categorized as either a Transformation or an Action.

Transormations are nothing but functions that take can be applied on an RDD and produces another new RDD. You can relate transformations to [**Immutability**](/distributed-processing-patterns/#immutability), [**Iterator**](/distributed-processing-patterns/#iterator-pattern), [**Pure Functions**](/distributed-processing-patterns/#pure-functions) and [**Pipelining**](/distributed-processing-patterns/#pipelining-or-keep-it-flowing) patterns of the previous post.

Sequence of transformations create a sequence of RDDs which become a Directed Acyclic Graph(DAG). Each RDD in the graph becomes a node and has one or more RDDs as parent nodes. Transformations are represented as edges of the DAG. 

The documents flow from top most RDD(root node) till the bottom most RDD(leaf node) and under go all those transformations defined as edges. This is where [**Iterator**](/distributed-processing-patterns/#iterator-pattern) and [**Pipelining**](/distributed-processing-patterns/#pipelining-or-keep-it-flowing) patterns come into picture.

Important observation here is that, unless somebody tries to pull a document from a child RDD, the child RDD will not as the parent RDD for a document and thus, the edge connecting these two RDDs will not be executed. This means, **that transformation edge will not be executed at all!** So, **if the DAG is not connected, the flow of documents will be disconnected** and the documents will not be processed at all! That is what [**Lazy evaluation**](/distributed-processing-patterns/#lazy-evaluation) is about as discussed in the previous post.

**Actions** are functions that **eagerly wait and consume all the data** and act like a sink. Usually actions are **do not pipeline** the documents. Instead, they try to accumulate the documents. Functions like *count*, *collect*, *saveAsTextFile*, *aggregate* etc are actions. 

In the DAG of RDDs, leaf nodes should always be used/consumed by at least one action. Because of **eagerness** of actions, they are the ones who try to pull the documents from DAG of RDDs, all the way up to the root RDD(or source RDD). Without actions, because of [**Lazy evaluation**](/distributed-processing-patterns/#lazy-evaluation), nobody tries to pull the data and the whole DAG of RDDs will not be executed at all!

#### Associative operations

The famous hello-world application of distributed processing systems doesn't make much of a sense(at least for a beginner) until one understands the reason and power of [**Associative operations**](/distributed-processing-patterns/#associative-operations). Designing the Spark applications to make use of associative operations where-ever possible is challanging, but most rewarding.

#### Bringing the Code to Data

The combination of Apache Spark and HDFS is a brilliant one when it comes to scalability. HDFS has all the information about how data is distributed across nodes and scheduler of Apache Spark makes smart decissions on where to run the code that processes the data. More about [bring the code to data](/distributed-processing-patterns/#bring-the-code-to-data) on my previous post.

All the transformations are [**pure functions**](/distributed-processing-patterns/#pure-functions). This is one of the importent characteristics because that is what makes Apache Spark **reliable and resilient to failures** as described in my previous post.

## Key points about scalability

How do we scale the Spark application? As I have already pointed out, **higher the number of partitions, faster is the processing**. But increasing the number of partitions is just one knob that controls the scalability.

The cluster on which the Spark application runs, should have capacity in terms of both memory and CPU. The disk/storage capacity can also become a bottolneck in some extreme use cases of Apache Spark because, the data that cannot be stored in memory, will be spilled to secondary storage. 

In summary, below are the key components that control the scalability:

- Number of partitions in each of the RDDs present in the DAG of RRDs(that is your application)
- Total memory and CPU resources available in the cluster as a whole
- Redundent actions and transformations will unnecessarily slow down the applicatioin
- Optimal use of caching/persisting of RDDs
- Key configurations of memory, CPU cores, shuffle, memory overhead etc according to available resources and application type(memory intensive/CPU intensive)
- Use of associative operations where-ever possible

## Common pitfalls

Below are the common mistakes that most of the beginner and intermediate level developers do 

* Collecting/accumulating the data in memory by use of actions like *collect*, *count* etc
* Not caching/persisting the data at appropriate stages of the DAG
* Not partitioning the data to improve the data distribution
* Not using the actions that do not trigger the costly shuffle operations

## Summary

This post mostly covers my experience with Apache Spark by considering my previous post on [Distributed processing Patterns](/distributed-processing-patterns/) as a reference. The post covers some of the interesting and important patterns that I have seen in distributed systems. Please feel free to contact me about any corrections required to the content. Happy coding!

### References

- https://techvidvan.com/tutorials/features-of-hdfs/
