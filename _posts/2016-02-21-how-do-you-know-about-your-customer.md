---
layout: post
title: How much do you know about your customer?
categories: data analytics
description: "Here are things you should know for your customer insight. RFM, survival analysis and customer life-time-value, LTV. How could I compare survival analysis with customer strategy? Let's find out."
comments: true
tags: [data analytics, data mining, kaplan meier curve, survival analysis]
image:
  feature:
  credit: 
  creditlink: 
date: 2016-02-21T00:00:00+00:00
author: ericali
---

Imagine you run your own business, for example, a coffee shop, or restaurant. What are your thoughts on your customers? How loyal are your customers? Who are the one-time buyers? Especially for promotional strategies, you have to spend every worth penny, right?

![A cup of coffee](/img/blog/erica/b02_coffee_shop.png)

### **Customer Profile**

Without customers your business would not exist. Are there any method assisted you to know your customer better? You have to know RFM first. 

What is RFM? Radio FM? Absolutely not! RFM is a classic marketing tool to identify your best customers. With RFM, you could identify new versus returning customers, check the most frequent customers, and how much your average customer spends. So, what are the definition?

- **R**ecency: How recently do the customer purchase?
- **F**requency: How often do they purchase?
- **M**onetary: How much do they spend?

![Time to Event](/img/blog/erica/b02_DF_cancer_KM.png)

### **Survival Analysis**

Mmm, sounds familiar. Did you remember our lastest blog?

[Kaplan-Meier Curve in Python](http://www.elasticmining.com/post/2016-01-01/kaplan-meier-curve.html)

Survival analysis is not just used in clinical trials, but in customer insight. Some of you must be confused. How so? 

Survival analysis is a set of a biostatistical process for the outcome of interest is time until an event occurs. For the application to customer behavior analysis, you could regard as a customer moves from active into inactive. A typical example is when customers could churn in the e-commerce.

Same methodology, two different industries.

### **Lifetime Value**

Now, you must know RFM. Let me introduce another marketing term to calculate the lifetime value, LTV. In general, LTV is a roughly revenue that a customer will generate during their active lifetime, perhaps, your shopping mall.

How to calculate LTV? Basically, we have to define some variables for the domain knowledge. In the online store, for example, we could know something from our dataset.

- **P**rofit margin per customer
- Customer **r**etention rate: the percentage of customers who repurchased to the number at risk
- **L**ifespan: 5 years, for instance
- **A**verage customer value per week
- ...

There are plenty methods to calculate LTV. How to estimate the maximum cost of a new customer? How to customize your own LTV per year? Maybe, you would like to try the simplest LTV equation like this,

$LTV=52(A)*t$ 

However, this is not the only one method to define your LTV. We would introduce more in the next blog. Once you get the LTV from your data, you boss could start to make strategies of a newbie customer.

### **Summary**

This is just the beginning. You could generate LTV, on the other hand, you may boost LTV by customer satisfaction. How to evaluate your customers with these weapons? Actions speak louder than words. Try ElasticMining, we could assist you to identify the best way to deliver with the most valuable customers, VIPs. Contact us!

### **Reference**

1. Wikipedia contributors. "RFM (customer value)." Wikipedia, The Free Encyclopedia. Wikipedia, The Free Encyclopedia, 12 Nov. 2015. Web. 7 Jan. 2016.

