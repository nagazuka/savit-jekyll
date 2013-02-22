---
layout: blogpost
title: "First look into Apache Hadoop and Oracle Coherence"
date: 2013-02-20 14:37
comments: true
tags:
- Big Data
- Hadoop
- Oracle Coherence
categories: 
---
With all the Big Data hype buzzing around in the industry, my curiosity about it has been growing steadily. I have started to look into two products in more detail to find out what the all the fuzz is about, and whether it would make sense to invest some time in it.

Because of my Java/Oracle background, I choose to check out two products:

*   **Apache Hadoop**: popular open-source project enabling MapReduce algorithms to be run easily on COTS hardware
*   **Oracle Coherence**: commercial data-grid product with seamless data distribution and MapReduce functionality which I have encountered at multiple Oracle-minded clients  
  
### First thoughts on Hadoop

#### General overview
Very flexible system that includes a whole ecosystem for distributed processing, from MapReduce for distributed computation, to HDFS for the distributed data storage and retrieval. In addition, there are a whole bunch of separate projects like Hive and Zookeeper, which build on the Hadoop core framework. 

#### Programming model
Hadoop seems to be very much file-based, and is using its own Java primitives that can be serialized by the framework, instead of the standard Java String, Integer, etc. Although Hadoop removes the difficulty of distributing the workload and data storage, it does seem like a lot of custom work is required to get your programs written using the file-based system with Hadoop serialization. 

#### Setup
Setting up Hadoop is also far from straightforward, mainly because of all the moving parts. This is probably the reason that Cloudera and Hortonworks are making good money selling Hadoop-management tools.

### First thoughts on Coherence

#### General overview
Coherence provides a distributed data-grid, but also allows for distributed computation. However, the focus seems to be on data storage for the purpose of caching, which is what I have seen it being used for in a few big companies. Even Oracle themselves use it as a caching layer for their Weblogic and Service Bus products.

#### Programming model
Coherence works with normal Java POJO objects, which can be stored and retrieved in the data grid like any other caching product. It uses its own serialization, but this is mainly transparent for the user. 

#### Setup
Very easy to get started with Oracle Coherence, simply setting the JAVA_HOME and running one of the .cmd or .sh files starts a node. With multicast setup as default, nodes are discovering and joining clusters in the network automatically.

### Next steps
In subsequent posts I will delve into detail on both products by solving the same problem using both products, to discover the good things and the pain points on Hadoop and Coherence. 

