---
layout: post
title: How to implement function composition in scala
categories: scala
description: "We are going to discuss how to define function in Scala, and also practice mathematical function composition. I hope you will know how beautiful the Scala code is."
comments: true
tags: ["scala","functional programming"]
image:
  feature:
  credit: 
  creditlink: 
date: 2015-12-21T00:00:00+00:00
author: markyang
---
# How to implement function composition in scala

## What’s function composition

This is the definition from wiki－*"In mathematics, function composition is the [pointwise](_https://en.wikipedia.org/wiki/Pointwise_ (https://en.wikipedia.org/wiki/Pointwise)) [application](https://en.wikipedia.org/wiki/Function_application) of one function to the result of another to produce a third function."*
You can also say that a parameter of such function is another function, for example

$$\sum_{a}^{b}g(x)$$

this is a summation fucntion, a, b, and g(x) are parameters of the function. We can show like this as a function

$$f(a,b,g(x))=\sum_{a}^{b}g(x)$$

a and b are variables,  g(x) is a function,  so *f(a,b,g(x))* is a function composition.

## Functions in Scala

*I=>O* means a function that I as a type of parameter and O as a type of output in Scala, like

```scala
val f1:Double=>Double=
		x=>Math.pow(x,2) 
		//f1(x)=x^2. e.g. f1(2)=4 
val f2:(Double,Double)=>Double=
		(x,y)=>Math.pow(x,2)+Math.pow(y,2) 
		//f2(x)=x^2+y^2 e.g. f2(2,3)=13
```

x and y can be replaced by any identifier you define.
Now some people may have known how to implement function composition, but before that, we can practice summation function *f(a,b,g(x))* by using recursive method.

```scala
def f(a:Int,b:Int,g:Int=>Int):Int={
if (a>b) 0
else f(a+1,b,g)+g(a)
}
```

>Note 
The types of the parameters and the output of g(x) are all integers (Int=>Int).


Now we can implement the summation fucnction form 1 to 10, 
$$f(1,10,g(x))=\sum_{1}^{10}g(x)$$

```scala
val constant:Int=>Int=x=>x

val pow:Int=>Int=x=>Math.pow(x,2).toInt //pow is a function that Int to Int


f(1,10,constant)

f(1,10,pow)
```

Wait! That's all?? Of course not, we can represent this function in a more concise way in Scala

```scala
val fc: ((Int) => Int) => Int= f(1,10,_)
//or
val fc=f(1,10,_:Int => Int)
```

Here we use “_” to represent  g:Int=>Int. Type of *fc* is ((Int) => Int) => Int, we can think *fc*’s parameter as a type of (Int)=> Int, the function of output Int. The benefit is decreasing repeat of codes.


```scala
fc(constant) //as like f(1,10,constant)

fc(pow) //f(1,10,pow)
```

>Note
The line val fc=f(1,10,_) would fail. you need to specify type of `_`, because compiler can not recognize it.

## Summary
Now we know how to define a function in Scala, and practice summation function. Then we use amazing _ in Scala to implement mathematical function composition.
Hope they are useful for you.

### Refrerences
[Scala Partially Applied Functions](http://www.tutorialspoint.com/scala/partially_applied_functions.htm)




