---
layout: post
title: Currying in Scala
categories: scala
description: We will talk about how to achieve currying and combine partially function, so we can make Scala more powerful!!
comments: true
tags: [scala, currying, function programming]
image:
feature:
credit:
creditlink:
date: 2016-2-1T00:00:00+00:00
author: markyang
---
# Currying in Scala



In [HOW TO IMPLEMENT FUNCTION COMPOSITION IN SCALA](http://www.elasticmining.com/post/2015-12-21/how-to-implement-function-composition-in-scala.html), we have learned the function--

$$f(1,10,g(x))=\sum_{x=1}^{10}g(x),\ where\ g(x)=x\ or\ x^2$$
Here's the code.

```scala

def f(a:Int,b:Int,g:Int=>Int):Int=

{

 if (a>b) 0

 else f(a+1,b,g)+g(a)

}

val fc =f(1,10,_)

```


```fc``` is also called ```partially function```. We usually mention partially function and currying at the same time, because these two can combine while coding, and makes your code more simple and elegant.
Today, I would like to share 3 things with you.

*  What is currying?

*  Currying in Scala.

*  Currying ＋ partially function

## What is currying?
*"Currying is the technique of translating the evaluation of a function that takes multiple arguments (or a tuple of arguments) into evaluating a sequence of functions, each with a single argument."* -- Wikipedia.


In other words, currying translate a multiple parameters function into a sequence of single parameter functions,  for example,

```scala
def sumOfSquares(x:Int,y:Int):Int=(Math.pow(x,2)+Math.pow(y,2)).toInt
```

p.s. It is unnecessary using return type of sumOfSquares ```Int```; however, I will demonstrate currying ＋ partially function in the end.

The process of transforming sumOfSquares(2,3) into sumOfSquares(2)(3) is currying.

On the contrary, we called uncurrying.


## Currying in Scala.

There are two ways to create currying function in Scala. The first one is declaring ```def f(arg1:Type)(agrg2:Type)``` , like this,

```scala

def sumOfSquares_Currying(x:Int)(y:Int):Int=(Math.pow(x,2)+Math.pow(y,2)).toInt

sumOfSquares_Currying(2)(3)

```

The other way is based on existed function, like this code,


```scala

val ss:(Int, Int) => Int=sumOfSquares_Currying(_:Int)(_:Int)

val ss_curried=ss.curried

```


See, quite simple! Now we have known how to create currying function in Scala. But how we combine partially function ?



## Currying ＋ Partially

### Using Partially in Currying.

If you want to use partially in currying, substitute unspecified parameters by ```_```, like the other normal functions. One different thing is that in normal functions, we must declare the type of ```_```, or it fails. Howerever, we don’t need to declare it in currying function.


```scala

sumOfSquares_Currying(2)(_) //OK

sumOfSquares_Currying(2)(_:Int) //OK


sumOfSquares(2,_)

//error message: missing parameter type for expanded function ((x$1) => sumOfSquares(2, x$1)) sumOfSquares(2,_)

sumOfSquares(2,_:Int) //OK
```

### Using Currying in Partially

Try to think about how we use curryung to fulfill function *h*.




*f(1,10,g(x,2))* is the partially function, ```val fc =f(1,10,_:Int=>Int)```, we mentioned at the beginning. Function g is ```sumOfSquares_Currying``` that shown previously. Now let’s see how to merge ```fc``` with ```sumOfSquares_Currying``` by one commend.

```scala

fc(sumOfSquares_Currying(2))

```


The type of ```fc``` is ```(Int => Int) => Int```, and ```sumOfSquares_Currying(2)``` is ```Int => Int```; thus, we use ```sumOfSquares_Currying(2)``` as parameter of ```fc``` to implement equation that we stated above.

## Summary

In this artical, we talked about currying & partially function, and how they are merged together. You can start to realize the fun part of Scala - flexibility, one traits of Scala!


Please leave your comments for anything about Scala, I’ll see you next time!!

## Reference
* [HOW TO IMPLEMENT FUNCTION COMPOSITION IN SCALA](http://www.elasticmining.com/post/2015-12-21/how-to-implement-function-composition-in-scala.html)
* [Currying - Wikipedia](https://en.wikipedia.org/wiki/Currying)
* [The Neophyte's Guide to Scala Part 11: Currying and Partially Applied Functions](http://danielwestheide.com/blog/2013/01/30/the-neophytes-guide-to-scala-part-11-currying-and-partially-applied-functions.html)



