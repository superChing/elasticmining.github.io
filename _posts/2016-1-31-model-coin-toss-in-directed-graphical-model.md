---
layout: post
title: Model Coin Tossing in Directed Graphical Model
categories: machine-learning
description: Probabilistic Graphical Model is a branch of machine-learning. Learning different view of machine-learning is helpful to understand the foundation. The post is about the Graphical Model and should give you the view based on statistics and graph theory.
comments: true
tags: [machine-learning, probability]
image:
feature:
credit:
creditlink:
date: 2016-1-31T00:00:00+00:00
author: superching
---

# Model Coin Tossing in Directed Graphical Model

## What is Directed Graphical Model

Directed Graphical Model is a class of [Probabilistic Graphical Model](https://en.wikipedia.org/wiki/Graphical_model).  A one-liner to describe it is — a graph representation of a distribution.

Directed Graphical Model is a pair $(P,G)$ where $P$ is a distribution and $G$ a graph associated with $P$. The insight of a distribution $P$ from the graph $G$ are two aspects [^1]:

- Factorization

  How a distribution can be decomposed. In academic way, we say $P$  can be factorized over graph $G$ .

- [Conditional Independence](https://en.wikipedia.org/wiki/Conditional_independence
)

  Different factorization turns out to have different conditional independence properties. In academic way,  we say $P$ satisfies the Conditional Independences associated with $G$.

Both the above of informations can be easily summarized by a graph structure $G$. From the graph we gain some benefits — the conditional independence viewpoint is useful for designing models, and the factorization viewpoint is useful for designing inference algorithms.

## A coin example

![screenshot](/img/blog/wayne/coin toss.png)
{: style="color:black; font-size: 110%; text-align: center;"}
Figure 1  IID samples of random variable $x$.  
{: style="color:black; font-size: 110%; text-align: center;"}

The notation $x$ is outcome of a coin, either head or tail, $θ$ is unknown coin bias. We toss $m$ times and get $m$ outcomes.

The graph says we can encode the node $θ$ with our prior knowledge of coin fairness in a probability distribution. We can also encode the arrows (from θ to $x$) with the probability distributions of a outcome, thus each toss induce an arrow. Finally we can create a joint probability distribution over all variables according to factorization property of the graph -

$$p(θ)\prod_i p(x_i|θ)$$  

and that also satisfy the conditional independences encoded in graph.


#### Conditional Independence assumptions

From the graph we can observe more information about the model, for example, the conditional independence properties.

First we need to know some basic rules. There are many conditional independence structures the graph can encode. Among them four are the most general, as shown in Figure 2.

​![screenshot](/img/blog/wayne/CI structure.png)
{: style="color:black; font-size: 110%; text-align: center;"}
Figure 2 the four primary structures that encode conditional independency.[^2]
{: style="color:black; font-size: 110%; text-align: center;"}

Thank to the simplicity of coin toss, here we only need the structure at Figure2(c) which is called *common cause structure*. Common cause structure has a Conditional Independence property that:

- the $X$ is independent of $Y$ if $Z$ is observed.  
- or $P(X\|Z,Y)=P(X\|Z)$

Back to the graph of coin toss model we may observe that the outcomes are not directly connected of each other. However it does not mean they are independent. Each two arrows introduce the *common cause structure*, which says  outcome $x_i$ is independent of outcome $x_j$ given θ is known. Or the other way around, the outcomes are probably *dependent* if the cause, here the bias of coin, is unknown .

You may ask how come they are dependent since we toss coin regardless of previous tosses. The explanation is if we do not known about coin fairness, then the tosses are dependent. Each toss tell us something about the unknown coin bias and thereby something about next toss. In contrast, once the fairness is known, we cannot learn anything about the outcome of next toss from previous observations.

## summary

Now have you found what benefit of this special graph gives? The properties of the graph help us formalize the independence assumption. With a glance of the graph you get information about how the data is generated and the model assumptions the author encoded. When the model becomes bigger and more complex, the graph will ease model interpreting.

------

[^1]: THEOREM.3.1 Koller, Daphne, and Nir Friedman. *Probabilistic graphical models: principles and techniques*. MIT press, 2009.
[^2]: Figure17.3 Koller, Daphne, and Nir Friedman. *Probabilistic graphical models: principles and techniques*. MIT press, 2009.
