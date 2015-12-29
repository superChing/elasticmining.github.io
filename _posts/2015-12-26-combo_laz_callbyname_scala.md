---
layout: post
title: Combine lazy and call-by-name into lazier Scala
categories: scala
description: Combining lazy value and call-by-name you would get lazier value to solve a cyclic reference problem.
comments: true
tags: [scala, functional programming]
image:
feature:
credit:
creditlink:
date: 2015-12-28T00:00:00+00:00
author: superching
---

#  Combine lazy and call-by-name into lazier Scala
Here it is a puzzle : A parent owns a child which owns that parent. How to get the *cyclic reference* in *immutable* way?  
Before answering it, we see how easy to program it in mutable way:

```scala
class Parent {var child:Child = null}
class Child {var parent:Parent = null}

val parent: Parent = new Parent
val child: Child = new Child

parent.child = child
child.parent = parent
```

Now the harder part is how do you program it in immutable way? To be immutable, mutable `var` must not exist. So we use immutable `val` that has to be initialized to the right value:

```scala
class Parent {val child = ???}
class Child {val parent = ???}
```
When we instantiate first one, the second one has't instantiated so nothing we can refer and we can not mutate the reference latter because value is immutable. How come we could make them depend each other once for all.       

---
Scala is powerful enough to have you define cyclic reference in immutable way. Key finding is that we could make the value flow lazily, both at constructor parameter and member definition. In short, we want to put off work as late as possible so that the value will be initiated to right reference once all reference is ready.  
One could make use of *non-strict evaluation* to achieve it. There are many [evaluation strategies](https://en.wikipedia.org/wiki/Evaluation_strategy) one could apply, and the *strict-evaluation* is what we usually do -- any work is done as soon as possible. *non-strict evaluation* is another class of evaluation strategies -- any work is done as late as possible. The non-strict evaluation may not strange to you, Iterator is an example.
We will use two of them:

- First is *call-by-name* `=>`, that has the parameter lazy, in a way that the value is re-evaluated every time you ask for them.
- Second is *lazy value* `lazy val`, that has the value member lazy, in a way that the value is evaluated the first time you ask and then the value is cached.

Combine the two lazy modifier, `lazy val` and `call-by-name`, would introduce a mini pattern:

```scala
def foo[T](x: => T) = { // => indicates call-by-name
  lazy val cache = x    // lazy indicates lazy value
}
```
The effect of the pattern is that if your code never takes it to need x at all, then x will never get evaluated. You need both to achieve this. Taking out either one would end it being evaluated, either at parameter part or member part. If the syntax is a bit of verbose to you, there's a [JIRA](https://lampsvn.epfl.ch/trac/scala/ticket/240) discussion of adding a new syntax("lazy parameters") for it.

Put the pieces together you get:

```scala
class Child(parent: => Parent) {lazy val parent = parent}
class Parent(child: => Child) {lazy val child = child}

val parent: Parent =  new Parent(child)
val child: Child = new Child(parent)
```

---
This example is just toy case. In practice, we sometimes encounter the situation of cyclic reference. For example, I ever at a time implemented an NLP tool that I need to create data model on sentences and words, where sentence refer to word inside, word refer to sentence containing it.
In practice however we don't need to stick to immutable for many reasons. Rather, I consider this as an invaluable puzzle to learn at once the two lazy constructs of functional programming -- *lazy evaluation* and *call-by-name evaluation*.
