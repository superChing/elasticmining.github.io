---
layout: post
title: The Structure of JVM
categories: programming
description: "If you want to be a beeter java developer, this article will help you to know more details in a java process."
comments: true
tags: ["jvm", "class loader", "execution engine", "run-time data areas"]
image:
  feature:
  credit: 
  creditlink: 
date: 2015-12-19T00:00:00+00:00
author: larrylo
---
# The Structure of JVM

In the previous article [An Introduction to JVM](/post/2015-12-08/introduction-to-jvm.html), we roughly introduce how a java process work from source code to final result. To understand  more details about JVM, we work on the JVM specification of [Oracle Java SE 7](https://docs.oracle.com/javase/specs/jvms/se7/html/index.html) to extract the main components of a java process. The following figure is a java process workflow for reference.

![An overview of a java process](/img/blog/larry/20151219/an_overview_of_a_java_process.png)

Because we have already shown the compiled procedure of a java process, in this article we start from the .class file to consequent steps.

## Class File

The compiled code from source file is represented as a binary format. If you want to see the execution steps of bytecode in a human-readable format, you can type the following command with our example in previous article.

```
$ javap -verbose HelloWorld.class
```

After that, the following contents are printed. In this article, we just present the result of disassembled .class file because we hope people notice the structure of JVM first before interpreting it. After understanding the overview of JVM, we will be more impressed when going back to read this *Classfile*.

![A readable bytecode part1](/img/blog/larry/20151219/a_readable_bytecode_p1.png)

![A readable bytecode part2](/img/blog/larry/20151219/a_readable_bytecode_p2.png)

## Class Loader

Java provides a dynamical loading feature at run-time to load the class if needed. There are three main class loaders including **Bootstrap class loader**, **Extension class loader** and **Application class loader** created with a JVM. Bootstrap class loader is implemented with native language like C. It is the parent of all class loaders and load basic Java APIs to search directory under *JRE/classes*. Extension class loader loads the classes or jars under *JRE/lib/ext* by default if needed. After a JVM inits Extension class loader, Bootstrap class loader will be referenced as the parent of Extension class loader. Application class loader will search the $CLASSPATH or via -cp option to check if there is any defined class needed to be loaded and references Extension class loader as its parent. Users can also build their own Class Loader under different circumstances. The most popular example is that J2EE uses Web-App class loader to load classes from WAR files. Before a class loader loads a class, it will search its parents to check if the class has been loaded or not to prevent duplicated loading. The following figure shows the features of class loader.

![class loader](/img/blog/larry/20151219/class_loader.png)

## Run-Time Data Areas

JVM defines various data areas used during the execution of a program. There are two types of life cycles about these data areas.One is shared between all threads within a JVM. The other one is created and destroyed in each thread life cycle. 

* Shared between all threads
    * Heap 
        * A JVM has a *heap* shares for all threads to use. It allocates for all class instances and arrays. All objects are only removed by **Garbage Collector** which we will discuss more detail at the later article.
        * If a computation requires more resource than the automatic storage management system can supply, the JVM will throw an **OutOfMemoryError** exception.
    * Method Area
        * The *method area* stores per-class structures such as the run-time *constant pool*, field and method data.
        * If memory in the method area does not be satisfied to requirement, the JVM will throw an **OutOfMemoryError** exception.
* In each thread
    * pc Register
        * If the current running method is not a *native* method, *pc* will address it into register.
    * JVM stacks
        *  A JVM stack stores *frames*, which is pushed to the top of the stack when invoking each method and popped out when returning a result or throwing a exception. Because a JVM stack is never manipulated except to push and pop frames, the memory of a JVM stack does not need to be contiguous.
        * If the required stack is more than its permission, the JVM sill throw an **StackOverflowError** exception.
        * If JVM stacks can be dynamically extended but insufficient memory for requirement, the JVM will throw an **OutOfMemoryError** exception.
    * Native Method Stacks
        * If a JVM needs to invoke *native* methods, which is written in a language instead of Java but like C language, a thread will or will not create a *Native Method Stack* depends on JVM implementation supplied or not. 
        * If the required stack is more than its permission, the JVM sill throw an **StackOverflowError** exception.
        * If JVM stacks can be dynamically extended but insufficient memory for requirement, the JVM will throw an **OutOfMemoryError** exception.

![Run-time data areas](/img/blog/larry/20151219/run_time_data_areas.png)

## Execution Engine

The java bytecode is an intermediate code communicated between people and JVM. In order to execute bytecode on a machine, a JVM use two ways , **Interpreter** and **JIT-compiler**, for this purpose by *Execution engine*. 

* Interpreter
    * The execution engine reads, interpreters and executes each bytecode instructions one by one. Though it can interpret bytecode quickly, it executes slowly the interpreted result.
* JIT-compiler
    * To overcome the disadvantage of interpreter, the JIT-compiler will compile the frequently used method to native code and store it in the cache. After that, all other commands associated with this method will not interpret directly but execute it in the cache.

However, the compiling time is longer than interpreting codes one by one. In order to optimize the execution engine, you have to consider what is the execution scenario of your program. If your program just runs once, the elapsed time of interpreting may be shorter than compiling.

## Summary

In this article, we introduce the workflow of a java program and some important components in a JVM. After compiling a .java file into bytecode, user can use javap to read bytecode or use java command to execute a java process. At the beginning of a java process, the JVM will use three main class loaders to load classes under their own class path. After that, the JVM will allocate the memory space for run-time data areas to execute. In addition, the JVM provides two ways, interpreters and JIT-compiler, to transform bytedcode to native code. In the end, the JVM will execute the native code to generate the final result. 

## Reference

1. [Understanding JVM Internals](http://www.cubrid.org/blog/dev-platform/understanding-jvm-internals/)

