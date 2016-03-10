---
layout: post
title: The First Class of Tableau - part 2
categories: data visualization
description: Learning Tableau step by step - map chart and dashboard .
comments: true
tags: [tableau, data visualization]
image:
feature:
credit:
creditlink:
date: 2016-3-10T00:00:00+00:00
author: bryanyang
---
# The First Class of Tableau-part 2

A few weeks ago, I introduced Tableau in [THE FIRST CLASS OF TABLEAU - PART 1](http://www.elasticmining.com/post/2016-01-18/the-fisrt-class-of-tableau-part-1.html), and completed a worksheet — a single chart, but it is not enough in a real case. Any business need dashboard, the combination of chart, to informs with a glance. A great business dashboard combines low latency and easy to use to let anybody get answers from the data. Tableau allows people who don't have any programming skills to create interactive, real-time visualization dashboard in just a few clicks. This article will lead you to do those things.

### The Second Worksheet and Map Chart

Tableau is very powerful in drawing all kinds of chart, especially the map chart.  

* First, your data **must** contain geo information. (You can provide Latitude-Longitude, State, City Name) 
![001](/img/blog/bryan/20160310/001.png)
* Change the property of the geo-variable is the right **Geographic Role**.
![002](/img/blog/bryan/20160310/002.png)
* Drag the **State** to the Raw, and drag the **Complaint ID** to the **Text**.
* Change the **Measure** to the **Distinct Count of Complaint ID**.
![003](/img/blog/bryan/20160310/003.png)
* Click **Show Me**, and select the map chart.

![004](/img/blog/bryan/20160310/004.png)

* Done!
![005](/img/blog/bryan/20160310/005.png)

### Create a Dashboard! 

Now we have two dashboards. We can create the first dashboard.

* Create one or more related worksheet
* Open a new dashboard

![006](/img/blog/bryan/20160310/006.png)

* Drop a worksheet from the left column to the center area

![007](/img/blog/bryan/20160310/007.png)

* Drop another worksheet  to the center area. 

![008](/img/blog/bryan/20160310/008.png)

* You could arrange those worksheets and change the size.
* If you want the two worksheets interacting with each others, you can do this:
    * The two worksheets came from the same data source
    * Move mouse to the edge of the worksheet which will be the filter
![009](/img/blog/bryan/20160310/009.png)
    * Click the downward arrow at the top right, and chose the **Use as Filter**
![010](/img/blog/bryan/20160310/010.png)
    * Now you can control the other worksheet by clicking the bar in the upon worksheet.
![011](/img/blog/bryan/20160310/011.png)

The full dashboard and more examples, you could see our dashboard on the **Tableau Public** [https://public.tableau.com/profile/elasticmining](https://public.tableau.com/profile/elasticmining)

### Reference

* [Magic Quadrant for Business Intelligence and Analytics Platforms, 2015](http://www.gartner.com/technology/reprints.do?id=1-2ACLP1P&ct=150220)

* [Tableu Offical Site](http://www.tableau.com)

