---
layout: post
title: How Garbage Collection Works in a JVM ?
categories: python
description: Garbage Collection is the most important part influenced the performance of Java. It will be helpful for developers to tune code if knowing more about the architecture of JVM.  
comments: true
tags: [jvm, garbage collection, memory generations]
image:
feature:
credit:
creditlink:
date: 2016-03-13T00:00:00+00:00
author: larrylo
---

In my first post of serial articles about JVM, The Structure of JVM (http://www.elasticmining.com/post/2015-12-08/introduction-to-jvm.html), we have explained the run-time procedure accross each components in a JVM. After understanding the basic knowledge about the structure of JVM, we can now dig into some important issues to help us make our java programs more efficient. The first thing I want to discuss is the garbage collection in a java program. 

**The Work Procedure of Garbage Collection**

Why a java program needed garbage collection? After a java process running for a while, there may be many memory fragmentation in heap size. To optimize the memory utilization and the performance of a java process, the garbage collection will automatically identify and release which object is unused and unreferenced. Generally speaking, the concept of garbage collection procedure can be integrated into three steps, **marking**, **deletion**, and **compaction**.

At the beginning, the garbage collection finds and marks all objects which were no longer in used or reference. After that, the marked objects will be deleted automatically based on the algorithm of garbage collection. To make a JVM allocate memory much easier and faster, the garbage collection will finally compact remained objects in memory space to a continuous list. The following figure describes the concept of a full procedure of garbage collection.

![gc-procedure](/img/blog/larry/20160313/gc-procedure.png)
**The JVM Generations**

Though we've noticed the concept of the procedure of garbage collection works, there are still more details in real situation. Among these details, the most impact on overall performance of a java process is that the garbage collection needs to pause the process before it starts to work. Imagine that, if a garbage collection often pauses the process, the elapsed time of the process will be raised more than expected result. To overcome this situation, the JVM heap is designed to three main generations for different purposes. They are **young generation**, **old generation** (**tenured generation**) and **permanent generation**.

At the time of a java process runs, all classes metadata are stored in permanent generation at run time and objects of classes will be all assigned to a part of young generation called **eden space**. Once the eden space is full, it will cause a **minor GC** to delete unreferenced objects and move referenced objects to another part called **survivor space**. The JVM designs two survivor spaces for copying and storing different aged objects for optimizing continuous memory address purpose. If you want to know more details about why we need two survivor spaces for storing aged objects, there is a great explanation (http://stackoverflow.com/questions/21476348/java-gc-why-two-survivor-spaces) you can check it. When the age of referenced objects reach a threshold, these objects will be promoted to old generation. Eventually, the old age objects will be removed from old generation by **major GC** and compact the space. I've arranged the interaction between JVM generations and different kinds of GC for people easily to understand the concept.

![jvm-generation](/img/blog/larry/20160313/jvm-generation.png)
**Summary**

In this article, I explain the concept of the main procedure how a garbage collection collects objects automatically including marking, deleting and compacting memory. This kind of design makes developers not to worry about when needing to release unreferenced objects. 

In addition to the simple actions of garbage collection works, we introduce why JVM segments memory space to three parts including young generation, old generation and permanent generation with its purposes respectively. Moreover, we explain the definitions of minor GC and major GC to make people understand how different kinds of GC works on each memory generation.

However, there are still many optimization issues we skip in this article. I hope not to give too much information at a time to prevent people feel chaos. In next article, I will tell more about when to use GC algorithms and how to tune them under different circumstances.

**Reference**

1. [Java Garbage Collection Basics](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html)


