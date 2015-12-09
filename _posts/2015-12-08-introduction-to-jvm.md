---
layout: post
title: An Introduction to JVM
categories: programming
abstract: "Java is a very popular programming language and has been used widely in varied domains. Every Java programmers always meets OOM problem when developing applications. In order to solve it, it's better to notice the JVM workflow to clarify problem."
comments: true
tags: []
image:
  feature:
  credit: 
  creditlink: 
date: 2015-12-08T00:00:00+00:00
author: larrylo
---

# An Introduction to JVM

Java is the most famous language in 2015. [1] It’s not only an open source programming language (i.e. OpenJDK, http://openjdk.java.net) but also the cross-platform  characteristic.

How Java can be used on multiple platforms like Windows, Linux or MacOS? The most important component to achieve this goal is JVM (Java Virtual Machine). A JVM is an abstract computing machine that enables a computer to run a Java program. There are three notions of the JVM: specification, implementation, and instance. [2] 

* **Specification**
    * A document that formally describes what is required of a **JVM implementation**. For example, the garbage collection algorithm used or the basic library . The following link is the Oracle SE 7 JVM Specification. (https://docs.oracle.com/javase/specs/jvms/se7/html/)

* **Implementation**
    * A JVM implementation is a program that meets the requirements of the **JVM Specification**.

* **Instance**
    * An JVM instance is a running program which is compiled into **Java bytecode**.

In this section, I will roughly explain every steps from create a java file, compile it, to run it on JVM.

### A JAVA PROGRAM WORKING FLOW

I will use a simple HelloWorld.java to explain how a Java code works.

```
class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World.");
    }
}
```

It’s a very simple code to print “Hello, World.” on console. Because this code can just be read for people but not for machine, you have to first compile “.java” file to “.class”  file (**Java bytecode**). The Java bytecode is a middle unit only can be read by JVM. Because of this characteristic, any OS,  which can run JVMs, can also be accepted Java bytecode build from anywhere. There is a simple figure to explain this scenario.

![A Simple Scenario](/img/blog/larry/20151208/1.png)

Now we start to compile “.java” file by using the following command. At this step, you can add the option, -verbose, to show each steps about how  “.java” file be compiled. 

```
$ javac -verbose HellowWorld.java
```

After you type javac command, the java compile will start to transfer the java high-level language to a series of bytes which can be read by JVM. 

![java to class](/img/blog/larry/20151208/2.png)

We can shrink all these steps to three main purposes:

1. The compiler will check if there is any syntax error, then find where the “.java” file is.

2. After that, the compiler will search if there is completely compiled  “.class” file to use.
 
3. Load related referenced class, for example, String.class.

After that, you can see there is a compiled "HelloWorld.class" at the same folder of your .java file.

![interprete](/img/blog/larry/20151208/3.png)

This “.class” file can be directly used to create a JVM by typing `$ java -cp . HelloWorld` to find the main function in your code and then you can see “Hello, World.”.

![Hello World](/img/blog/larry/20151208/4.png)

### The JVM OVERVIEW
Let us dig into the JVM architecture to know how it runs Java bytecode.

![JVM](/img/blog/larry/20151208/5.png)
*"JvmSpec7" by Michelle Ridomi - Own work. Licensed under CC BY-SA 4.0 via Commons - https://commons.wikimedia.org/wiki/File:JvmSpec7.png#/media/File:JvmSpec7.png*


Above figure is a standard JVM architecture. After user run Java bytecode, the **Class Loader**, a part component of **JRE** (Java Runtime Environment), is responsible for locating java libraries, reading their contents and loading all needed class file into JVM.

As I mentioned in the beginning of this article, JVM specifies many abstract features and can be implemented by different host architectures. These features of  JVM, like heap size, stacks, garbage collections will do their job and interpret all needed class files to machine code via **Execution Engine**. In the end, we will see the final result built in Java file.

### SUMMARY

In this article, we roughly explain full execution steps of how a Java program running on a host. To prevent confusing a java beginner, we did not put too many details into this article. However, to encourage more people being better java programmers, we will continue to learn and write a series of articles for those who has interest in this domain.

### Reference

[1] TIOBE Software, http://www.tiobe.com/index.php/content/paperinfo/tpci/index.html

[2] Wikipedia, https://en.wikipedia.org/wiki/Java_virtual_machine

