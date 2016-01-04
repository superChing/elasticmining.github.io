---
layout: post
title: Kaplan-Meier Curve in Python
categories: python
description: "Kaplan-Meier curve is one of the best survival analysis to display your target population living for a certain amount of time after treatment, especially in clinical trials."
comments: true
tags: [python, survival analysis, biostatistics]
image:
  feature:
  credit: 
  creditlink: 
date: 2016-01-01T00:00:00+00:00
author: ericali
---

# Kaplan-Meier Curve in Python

In this article, we introduce Kaplan-Meier (KM) curve and we show examples using Python. KM curve is one of the best survival analysis to display your target population living for a certain amount of time after treatment, especially in clinical trials. First, let me describe the type of analytic problem addressed by survival analysis. In general, survival analysis is a set of biostatistical process for the outcome of interest is **time until an event occurs**. 

![Time to Event](/img/blog/erica/b01_EventTime.png)

It is also used to evaluate the relationship between target variables and time-to-events. For example:

1. Time to death/relapse/recovery
2. How long can we expect our customer to leave website?

So, what is KM curve? The KM curve is defined as the probability of surviving in a given length of time while considering time in many small intervals. At most of time, the goal is to estimate target survival curve from a sample. From the KM curve, even you patients/targets drop out, it still estimates of the fraction surviving at each time.

### **What We Need for Kaplan-Meier Curve?**

Of course, we need **time** and **event**. By time, it could be months, days or years, from the beginning of follow-up of a person. What about event? It means death, or disease incidence, etc. At this time, you could plot your KM curve.

### **Kaplan-Meier Curve in IPython**

For the following analysis, we'll use *numpy*, *pandas*, *lifelines*, *matplotlib* and *pylab* for python.

```python
# python libraries we need
import numpy as np
import pandas as pd
import lifelines as ll

%matplotlib inline
import matplotlib.pyplot as plt

#larger
from pylab import rcParams
rcParams['figure.figsize'] = 10, 5

from lifelines.estimation import KaplanMeierFitter
```
The data contains the following columns,

- LENFOL: length of follow-up (days)
- FSTAT: loss to follow-up=0, death=1
- GENDER: males=0, females=1

Note, this data are subject to right-censored only. It means some target we don't know when they died after event, but we do know at least how many days they survived.

Graphs of the KM curve allow us to see how the survival time changes over time and are fortunately very easy to generate in python:

```python
kmf = KaplanMeierFitter()

T = df['LENFOL']
C = df['FSTAT']

#kmf.fit(observed_lifetimes, event_observed=C)
kmf.fit(T, event_observed=C)
kmf.plot(title = 'Worcester Heart Attack Profile 1')
```

Above, we have already specified a variable *df* that keeps the data in a pandas dataframe.

![Curve](/img/blog/erica/b01_KMall.png)	

The KM curve drops more steeply at the beginning of the study, suggesting that the hazard rate is highest immediately after hospitalization during the first 200 days.

```python
#male
f = df.GENDER==0
T = df[f]['LENFOL']
C = df[f]['FSTAT']
#female
f2 = df.GENDER==1
T2 = df[f2]['LENFOL']
C2 = df[f2]['FSTAT']

ax = plt.subplot(111)

kmf.fit(T, event_observed=C, label=['Male'])
kmf.survival_function_.plot(ax=ax)
kmf.fit(T2, event_observed=C2, label=['Female'])
kmf.survival_function_.plot(ax=ax)

plt.title('Lifespans of different gender')

kmf2 = plt.gcf()
```

![Curve](/img/blog/erica/b01_KMgender.png)

In the graph of the KM curve stratified by gender above, it means that females have a worse survival experience compared with male.

### **Summary**

We now briefly summarize this article. You realize how to compute KM curve given survival time and failure status from sample data and interpret a graph from KM curves that compare two or more groups, even one group. 

What's next? You could use the log-rank test of the null hypothesis from your KM curves. Seeing is believing, right?

### **Reference**

1. Altman DG. London (UK): Chapman and Hall; 1992. Analysis of Survival times.In:Practical statistics for Medical research; pp. 365–93.

